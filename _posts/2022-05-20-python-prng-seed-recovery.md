---
title: Attacking Python PRNG
tags: [prng, python]
style: border
color: primary
description: Python PRNG seed recovery
---

## Inspiration

I was asked to prepare some CTF challenges for [CCSC-2022](https://ccsc.org.cy/archive-2022/). I recently earned my blue belt in [pwn.college](https://pwn.college/) so they were expecting me to deliver some pwn exercises and I did. However, I always found PRNG seed recover attacks very facinating and I have only played around with Java and Android PRNGs, so I thought I'll do one now for Python.

## Background

What is a PRNG?

PRNG stands for Pseudo-Random-Number-Generator. PRNGs are designed with the intent of being able to generate sequences of numbers that have the same properties as sequences of random numbers. If you are interested in a more formal definition you can start your journey [here](https://en.wikipedia.org/wiki/Pseudorandom_number_generator).

The reason PRNGs are not actual RNGs is because they only seem to behave as RNGs for someone that is missing some information. This is very useful when the sources of randomness are limited and when true randomness is not necessary. For example, if you want to simulate the random behaviour of gas molecules, you usually wouldn't care if someone could predict the outcome of the simulation. If however you were generating your new BTC wallet you might be a bit more sceptical.

Some times PRNGs do not even behave as RNGs at all (e.g. have some periodic behaviour).

## Research

So I started digging a bit deeper into Python, and its PRNG implementation. One of the best resources I found was an [article](https://jazzy.id.au/2010/09/22/cracking_random_number_generators_part_3.html) by James Roper from back in 2010. He has a series of great articles regarding PRNG attacks. This was a great starting point.

So it turns out python (as well as some other languages) use Mersenne Twister PRNG algorithm and the recovery algorithm is well documented. At this point things were looking good, I just had to implement the recovery algorithm and **money**! But with a glimpse down below you can probably guess that was not the case.

The article was great in explaining how the attack should work, but was far from a complete implementation, and my understanding of the algorithm was still very poor. So I started looking into pythons random module implementation [here](https://github.com/python/cpython/blob/main/Modules/_randommodule.c).


## Mersenne Twister PRNG algorithm

The python function of interest is shown below 

```c
/* generates a random number on [0,0xffffffff]-interval */
static uint32_t
genrand_uint32(RandomObject *self)
{
    uint32_t y;
    static const uint32_t mag01[2] = {0x0U, MATRIX_A};
    /* mag01[x] = x * MATRIX_A  for x=0,1 */
    uint32_t *mt;

    mt = self->state;
    if (self->index >= N) { /* generate N words at one time */
        int kk;

        for (kk=0;kk<N-M;kk++) {
            y = (mt[kk]&UPPER_MASK)|(mt[kk+1]&LOWER_MASK);
            mt[kk] = mt[kk+M] ^ (y >> 1) ^ mag01[y & 0x1U];
        }
        for (;kk<N-1;kk++) {
            y = (mt[kk]&UPPER_MASK)|(mt[kk+1]&LOWER_MASK);
            mt[kk] = mt[kk+(M-N)] ^ (y >> 1) ^ mag01[y & 0x1U];
        }
        y = (mt[N-1]&UPPER_MASK)|(mt[0]&LOWER_MASK);
        mt[N-1] = mt[M-1] ^ (y >> 1) ^ mag01[y & 0x1U];

        self->index = 0;
    }

    y = mt[self->index++];
    y ^= (y >> 11);
    y ^= (y << 7) & 0x9d2c5680U;
    y ^= (y << 15) & 0xefc60000U;
    y ^= (y >> 18);
    return y;
}
```

Without going into the details of the algorithm lets have a look at the high level overview.

The PRNG keeps a *state* array (`self->state`) with **624** unsigned integer entries and an *index* variable (`self->index`) that points to an entry in the *state* array.

The index starts from zero all the way up to 623 and then wraps around to zero. To generate the next number a transformation is applied to the integer pointed to by the *index* variable in the *state* array and the index is incremented. When the index reaches the end of the array (624) a transformation of the *state* is applied and the *index* is wrapped back to zero.


The *state* array transformation can be seen here. Note that it is only applied when the index has reached the length of the *state* array `N`.

```c
    if (self->index >= N) { /* generate N words at one time */
        int kk;

        for (kk=0;kk<N-M;kk++) {
            y = (mt[kk]&UPPER_MASK)|(mt[kk+1]&LOWER_MASK);
            mt[kk] = mt[kk+M] ^ (y >> 1) ^ mag01[y & 0x1U];
        }
        for (;kk<N-1;kk++) {
            y = (mt[kk]&UPPER_MASK)|(mt[kk+1]&LOWER_MASK);
            mt[kk] = mt[kk+(M-N)] ^ (y >> 1) ^ mag01[y & 0x1U];
        }
        y = (mt[N-1]&UPPER_MASK)|(mt[0]&LOWER_MASK);
        mt[N-1] = mt[M-1] ^ (y >> 1) ^ mag01[y & 0x1U];

        self->index = 0;
    }
```

The state variable transformation that generates the next number can be seen here.

```c
    y = mt[self->index++];
    y ^= (y >> 11);
    y ^= (y << 7) & 0x9d2c5680U;
    y ^= (y << 15) & 0xefc60000U;
    y ^= (y >> 18);
    return y;
```

## Attacking the PRNG

So technically, if the full 32 bits of a generated number are obtained, then if the *state* variable transformation can be reversed, the state variable at the given index can be recovered. This means that if enough numbers are obtained the entire *state* array can recovered. 

Reading through James Ropers article we can see how the *state* variable transformation can be reversed. 

In order to play around with the algorithm and have more control while performing the attack a custom implementation was written in python based on James Ropers article. The *state* array transformation was implemented slightly different between the two sources but James Ropers implementation made more intuitive sense so I picked that one. I guess the python implementation is more efficient. Note that I had to make sure all numbers are up to 32 bits so I added the `& 0xffffffff` at the end.

```python
import random


state = list(random.getstate()[1][:-1])
idx = random.getstate()[1][-1]

def next_state():
    global idx
    if idx == 624:
        for i in range(624):
            y = ((state[i] & 0x80000000) + (state[(i+1) % 624] & 0x7fffffff)) & 0xffffffff
            next = (y >> 1) & 0xffffffff
            next ^= (state[(i+397) % 624]) & 0xffffffff
            if (y & 1) == 1:
                next ^= (0x9908b0df) & 0xffffffff
            state[i] = (next) & 0xffffffff
        idx = 0
    y = state[idx]
    idx += 1
    y ^= (y >> 11)
    y ^= (y << 7) & 0x9d2c5680
    y ^= (y << 15) & 0xefc60000
    y ^= (y >> 18)
    return y & 0xffffffff
```

Another detail here is that the python implementations *state* array seemed to have **625** entries which was a bit discouraging

```python
>>> import random
>>> len(random.getstate()[1])
625
```

but it turned out the last number was just the *index* variable

```c
    for (i=0; i<N ; i++) {
        element = PyLong_AsUnsignedLong(PyTuple_GET_ITEM(state, i));
        if (element == (unsigned long)-1 && PyErr_Occurred())
            return NULL;
        new_state[i] = (uint32_t)element;
    }

    index = PyLong_AsLong(PyTuple_GET_ITEM(state, i));
```

The reversing of the *state* variable transformation was then implemented.

```python
def unbitshift_right_xor(value, shift):
    i = 0
    result = 0
    while (i * shift < 32):
        part_mask = ((0x0ffffffff << (32 - shift)) & 0xffffffff) >> (shift * i)
        part = value & part_mask
        value ^= part >> shift
        result |= part
        i += 1
    return result


def unbitshift_left_xor(value, shift, mask):
    i = 0
    result = 0
    while (i * shift < 32):
        part_mask = ((0x0ffffffff >> (32 - shift)) & 0xffffffff) << (shift * i)
        part = value & part_mask
        value ^= (part << shift) & mask
        result |= part
        i += 1
    return result


def unapply_transformation(num):
    value = num
    value = unbitshift_right_xor(value, 18)
    value = unbitshift_left_xor(value, 15, 0xefc60000)
    value = unbitshift_left_xor(value, 7, 0x9d2c5680)
    value = unbitshift_right_xor(value, 11) & 0xffffffff
    return value
```

The only thing left now to get a working PoC is to run the reversing function multiple times, obtain the full transformation matrix, and use it to reset the random function or predict the next outcomes.

```python
import random


def unbitshift_right_xor(value, shift):
    i = 0
    result = 0
    while (i * shift < 32):
        part_mask = ((0x0ffffffff << (32 - shift)) & 0xffffffff) >> (shift * i)
        part = value & part_mask
        value ^= part >> shift
        result |= part
        i += 1
    return result


def unbitshift_left_xor(value, shift, mask):
    i = 0
    result = 0
    while (i * shift < 32):
        part_mask = ((0x0ffffffff >> (32 - shift)) & 0xffffffff) << (shift * i)
        part = value & part_mask
        value ^= (part << shift) & mask
        result |= part
        i += 1
    return result


def unapply_transformation(num):
    value = num
    value = unbitshift_right_xor(value, 18)
    value = unbitshift_left_xor(value, 15, 0xefc60000)
    value = unbitshift_left_xor(value, 7, 0x9d2c5680)
    value = unbitshift_right_xor(value, 11) & 0xffffffff
    return value


clone_state = []
rand_vals = []

init_state = [x for x in random.getstate()[1][:-1]]

for i in range(624):
    rnd_val = random.getrandbits(32)
    clone_state.append(
        unapply_transformation(
            rnd_val
        )
    )
    rand_vals.append(rnd_val)

orig = [random.getrandbits(32) for _ in range(2*624)]

random.setstate((3, tuple(clone_state + [624]), None))

clone = [random.getrandbits(32) for _ in range(2*624)]

print(orig == clone)

```


## Pitfalls

It is **VERY** important to note that this would only work if the first index used is **0** or a multiple of **624** and all **624** consecutive 32 bit numbers are obtained.

There are some workarounds for the cases some of which work better and some worse.

For example if the first number is not at index **0** we could continue generating numbers after our first 624 integers and always popping the oldest number in the array while adding the newly recovered state variable. At the same time we predict the next number and if a match was found we can have a high confidence that the state was recovered. The more numbers we predict the higher the confidence. If the numbers mismatch, we again pop the oldest number in the array and add the newly recovered state variable.

If not all 32 bits are obtained for each number then we could maybe try to recover multiple consecutive *state* arrays and work our way backwards since bits are mixed. This was not investigated in here at all and better solutions might exist.

Finally, if the numbers are not consecutive then it would heavily depend no which numbers are obtained. If a few consecutive numbers are obtained as well as their respective numbers at offset `+397 % 624` then we could likely predict those for a few more round before the reset of the unknown state values start corrupting the known. Again, this was not investigated in here at all and better solutions might exist.

Finally, while messing around with pythons different random number generators, it was noted that some generators such as `randint` would call the number generator multiple times.

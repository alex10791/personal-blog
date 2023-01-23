---
title: Floating Referenece Point Colorless Pip Count
tags: [Backgammon, pip-counting]
style: border
color: primary
description: An faster version of colorless pip count
---


## Background

Floating referenece point colorless pip count, uses a second symetry on the board to elliminate even more checkers during pip counting. 

Usually, when performing any kind of relative pip count, we can ignore checkers of opposite color that are on the same point but opposite site of the board. This is a symmetry that can be exploited to reduce the number of checkers that we need to count. Colorless pip counting is no exception. In reality, colorless pip count is not colorless, but instead colors are remaped in a way that preserve the race difference. So similar to any relative pip count, this symmetry can be exploited in colorless pip counting as well. We will refere to this symmetry as vertical symemtry. This article introduces another symmetry that can be used to elliminate even more checkers and will be refered to as horizontal symmetry.

In order to understand relative colorless pip count, one needs to understand how colorless pip counting works, so if you are not familiar, or need a quick refresher take a look at [this]({{ site.baseurl }}{% link _backgammon/2023-01-21-colorless-pip-counting.md %}) article.

## Floating Referenece Point

The idea of ignoring checkers on the opposite side of the board if they are of different color comes from the fact that both checkers have the same distance to cover to be born off. So what if we slide that point to somewhere else? What if we take that point and move it to lets say, both players 12 point? That means that checkers on the 12 point will be counted as "0 pips away", checkers on our 24 point will be counted "12 pips away", and checkers boren off will be counted "-12 pips away". Of course the relative difference does not change at all. This is very easy to understand since the only thing we did was to offset all checkers by -12 pips.

This has a few added benefits. First of all, we only count numbers up to 13 (bar point), which is easier to do. 

A second benefit is that since we can move the reference point anywhere we want, we can just pick a point with many checkers and have them all count as zero, effectively ignoring them all together! If we instead pick the 13 point as the reference point instead of the 12 we might have 14 pips to the bar point, but we can ignore all the checkers on the 13 point.  

The third and most significant benefit thought, is that we introduce another symmetry to the board, orthogonal to the first. At this point we can start elliminating same colored checkers that are the same distance away but opposite site of our reference point.

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">157</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_4.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_up_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_b_5.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_up_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:20px;"><img alt=""
                    src="https://apbg.net/board_images/b_center.png" style="width:267px;height:20px;" /><img alt=""
                    src="https://apbg.net/board_images/c_ce_1.png" style="width:24px;height:20px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_b_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_4.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_w_5.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">151</th>
    </tr>
</table>

Lets take a look at this position.

Using the method described above and picking the 13 point as the reference point, we can elliminate all checkers on the 13 point for both players immediately. Then we can elliminate the blue checkers on points 8 and 18 as they are both 5 pips away from the 13 point, and both have two checkers on them. We can also ignore the white checkers on points 3 and 20 as they are both 8 pips away from the 12 point (whites 13 point). So all we are left with to count are the checkers on blues 5 and whites 8 point. So we immediately see that this is 3 pips per checkers, so 6 pips.

The floating referenece point pip count is a very powerfull method by its self as it allows us to exploit a new type of symmetry which we call horizontal symmetry. The reason is that it allows us to exploit symmetries, not only in opposite player checkers, but also on same player checkers.

## Extending colorless pip counting

The floating referenece point pip count by its self can be a strong technique, but it shines when combined with colorless pip count. The reason is that when we use colorless pip counting, we get clusters of same colors which makes it even easier to find symmetry in. 

### Floating reference point colorless pip count

Lets look at an example.

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">144</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_5.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_up_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_b_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_up_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:20px;"><img alt=""
                    src="https://apbg.net/board_images/b_center.png" style="width:267px;height:20px;" /><img alt=""
                    src="https://apbg.net/board_images/c_ce_1.png" style="width:24px;height:20px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_b_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_w_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">143</th>
    </tr>
</table>

Lets start by changing the colors

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">105</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_5.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_up_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_up_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:20px;"><img alt=""
                    src="https://apbg.net/board_images/b_center.png" style="width:267px;height:20px;" /><img alt=""
                    src="https://apbg.net/board_images/c_ce_1.png" style="width:24px;height:20px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_b_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">104</th>
    </tr>
</table>

So now with little effort we can se that a very interesting floating referenece point is the 6-point. So now we can ignore all checkers on both 6-points.

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">75</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_5.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_up_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_up_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:20px;"><img alt=""
                    src="https://apbg.net/board_images/b_center.png" style="width:267px;height:20px;" /><img alt=""
                    src="https://apbg.net/board_images/c_ce_1.png" style="width:24px;height:20px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_b_3.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">86</th>
    </tr>
</table>

Now we can see that 5 and 7 are 1 pip away from our reference point on opposite directions, so we can elliminate an equal number of checkers on each side. Now it happens that there is 2 checkers on each point so we can elliminate them all. This is true for the 4 and 8 points and the 17 and 21 points. So lets remove those as well.

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">51</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_9.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_up_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_up_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:20px;"><img alt=""
                    src="https://apbg.net/board_images/b_center.png" style="width:267px;height:20px;" /><img alt=""
                    src="https://apbg.net/board_images/c_ce_1.png" style="width:24px;height:20px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_b_11.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">38</th>
    </tr>
</table>

A maybe slightly less obvious symmetry here is the 14 and 24 points, both are 5 pips away from the 6 point on each side, so they can also be elliminated.

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">37</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_11.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_up_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_up_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:20px;"><img alt=""
                    src="https://apbg.net/board_images/b_center.png" style="width:267px;height:20px;" /><img alt=""
                    src="https://apbg.net/board_images/c_ce_1.png" style="width:24px;height:20px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_b_11.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">38</th>
    </tr>
</table>

Now we have one vertical symmetry on both players 12 point. So we end up with the following position

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">1</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_14.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_up_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_up_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:20px;"><img alt=""
                    src="https://apbg.net/board_images/b_center.png" style="width:267px;height:20px;" /><img alt=""
                    src="https://apbg.net/board_images/c_ce_1.png" style="width:24px;height:20px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_b_14.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">2</th>
    </tr>
</table>

Finally we see that blue needs to move one pip forward to mirror whites position, so blue is trailing by one pip! Take note here that the reason blue is trailing by 1 pip is **not** because it needs one more pip to get to be boren off, but because it needs -5 to reach its 6-point while white needs -6 to reach its 6-point. This will be clear in the next example.

What we observed here is the exploitation of two different symmetries, one for same colored checkers (horizontal) and one for different colored checkers (vertical), in combination with the colorless technique that tends to cause clusters and benefiting both types of the symmetries. An added benefit is that all numbers are now up usually up to 7 pips away from the floating reference point.

### Floating reference point selection

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">156</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_4.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_up_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_4.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_up_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:20px;"><img alt=""
                    src="https://apbg.net/board_images/b_center.png" style="width:267px;height:20px;" /><img alt=""
                    src="https://apbg.net/board_images/c_ce_1.png" style="width:24px;height:20px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_b_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">126</th>
    </tr>
</table>

Now there is no nice vertical symmetry on the board or horizontal symmetry around the 6-point, however there is a beautiful horizontal symmetry around the 7-point so we can just use that. Lets start by separating the colors.

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">126</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_4.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_up_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_4.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_up_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:20px;"><img alt=""
                    src="https://apbg.net/board_images/b_center.png" style="width:267px;height:20px;" /><img alt=""
                    src="https://apbg.net/board_images/c_ce_1.png" style="width:24px;height:20px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_b_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">96</th>
    </tr>
</table>

Now lets apply horizontal point ellimination around the 7-point for both players.

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">55</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_10.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_up_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_up_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:20px;"><img alt=""
                    src="https://apbg.net/board_images/b_center.png" style="width:267px;height:20px;" /><img alt=""
                    src="https://apbg.net/board_images/c_ce_1.png" style="width:24px;height:20px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_b_13.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">5</th>
    </tr>
</table>

Now lets also apply vertical checker ellimination.

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">69</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_9.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_up_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_up_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:20px;"><img alt=""
                    src="https://apbg.net/board_images/b_center.png" style="width:267px;height:20px;" /><img alt=""
                    src="https://apbg.net/board_images/c_ce_1.png" style="width:24px;height:20px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_b_14.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">4</th>
    </tr>
</table>

And now lets just add the difference to the floating reference point. White needs 27 pips to get to it, and blue needs -3 pips, which gives a total of 30 pips lead for blue. In this example, unlike the previous one, it is clear that if we counted the race until the bare off point, the count would be off. So it is very important to keep in mind that the count is with respect to the floating reference point.

### Half-point example

In this example we will see a very interesting fact. That is that the floating reference point does not need to be an actual point, but could be a fractional point as well.

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">114</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_up_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_up_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:20px;"><img alt=""
                    src="https://apbg.net/board_images/b_center.png" style="width:267px;height:20px;" /><img alt=""
                    src="https://apbg.net/board_images/c_ce_1.png" style="width:24px;height:20px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_b_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">92</th>
    </tr>
</table>

In this example I have already separated the colors just to save some time.

In this case we can use the "6.5" point (**NOT THE BAR**) as a floating reference point, this has the disadvanage that we can not simply ignore checkers that lie on the floating reference point (and hence have a 0 pip offset) as it does not exist, but in some cases such as the above, it can create some beautiful symmetries. In this case we can rip-off both player primes immediately.

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">49</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_10.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_up_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_up_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:20px;"><img alt=""
                    src="https://apbg.net/board_images/b_center.png" style="width:267px;height:20px;" /><img alt=""
                    src="https://apbg.net/board_images/c_ce_1.png" style="width:24px;height:20px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_b_14.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">1</th>
    </tr>
</table>

And then also elliminate the vertical symmetry

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">48</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_11.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_up_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_up_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:20px;"><img alt=""
                    src="https://apbg.net/board_images/b_center.png" style="width:267px;height:20px;" /><img alt=""
                    src="https://apbg.net/board_images/c_ce_1.png" style="width:24px;height:20px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_b_15.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">0</th>
    </tr>
</table>

Now we just need to count the pip difference and keep in mind the half point offset. There can be a few mental tricks to do this faster but they are not necessary.

Some examples would be to pair checkers in two and account for their half pip as a pair or count two pips for each pip until before the half point and then add one and at the end devide by two. But such techniques are left for the reader to come up with.

In this case, white needs 22 pips to reach the floating reference point, so blue is leading 22 pips.

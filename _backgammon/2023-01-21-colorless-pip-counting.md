---
title: Colorless Pip Count
tags: [Backgammon, pip-counting]
style: border
color: primary
description: A fast way to pip count
---


## Colorless Pip Count

Colorless pip count is a very fast way to count the race difference. It seems confusing at first, but once we understand why it works its actually very simple.

The idea behind colorless pip counting is that we can just count the difference in the race of the closest 15 checkers to be boren off on each side and that is equivalent to the race difference no matter what the color of each checker is.

This might sound confusing at first so lets look at an example.

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

In this position blue leads by 30 pips.

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

In this position blue also leads by 30 pips. The checker placement is identical, but the colors have changed, yet the relative pip count is still the same. Why is this the case? Lets look at why this happens.

Lets start by assuming a simple position like the following. What will the change in pip count be if we just swap the colors of the two checkers?

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">15</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_14.png" style="width:25px;height:66px;" /><img alt=""
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
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_b_1.png" style="width:18px;height:66px;" /><img alt=""
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
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_w_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">14</th>
    </tr>
</table>

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">11</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_14.png" style="width:25px;height:66px;" /><img alt=""
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
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_1.png" style="width:18px;height:66px;" /><img alt=""
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
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_1.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">10</th>
    </tr>
</table>

It does not change, in both cases blue leads by 1 pip. The reason behind this is that in order to swap colors, we effectively moved the blue checker 4 pips ahead (towards blues home board) and moved the white checker 4 pips ahead (towards whites home board). So the relative pip count does not change. 

Using this primitive, we can go a step further and mentally "break contact" on the board. In other words, we can ignore the color all together, and just count the closest 15 checkers to blues home board as blue checkers, and the remaining 15 checkers closest to whites home board as whites checkers.

This technique has two added benefits over other relative pip counting techniques. 

First of all, the numbers we add are usually much smaller since its unusual to end up having checkers further than our opponents out field, and even when we do its very few and the pip count might not be very relevant. 

The second benefit, is that we can exploit symetries to a greater extend. The reason is that usually, in order to ignore checkers when doing a relarive pip count, we had to make sure that the checkers on the opposite site (opponents equivalent point) had our opponents checkers on it, not ours.

Consider the following board configuration.

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">113</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_up_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_up_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:20px;"><img alt=""
                    src="https://apbg.net/board_images/b_center.png" style="width:267px;height:20px;" /><img alt=""
                    src="https://apbg.net/board_images/c_ce_1.png" style="width:24px;height:20px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_b_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">110</th>
    </tr>
</table>

Using simple symmetry without the colorless thechnique, we can ignore the checkers on blues 2 and 23, 3 and 22, 4 and 21, 6 and 19 since they cancel out. So now we are left with counting the remaining 14 checkers. Now lets have a look at the coloreless technique.

<table>
    <tr>
        <th style="width:266px;text-align:left;">White</th>
        <th style="width:25px;text-align:right;">87</th>
    </tr>
    <tr>
        <td colspan="2">
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_high.png" style="width:291px;height:18px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_w_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_up_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_ud_w_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_up_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_up_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:20px;"><img alt=""
                    src="https://apbg.net/board_images/b_center.png" style="width:267px;height:20px;" /><img alt=""
                    src="https://apbg.net/board_images/c_ce_1.png" style="width:24px;height:20px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:66px;"><img alt=""
                    src="https://apbg.net/board_images/o_b_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/b_dn_0.png" style="width:25px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_b_2.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_b_3.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dn_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/p_dd_0.png" style="width:18px;height:66px;" /><img alt=""
                    src="https://apbg.net/board_images/c_dn_0.png" style="width:25px;height:66px;" /></p>
            <p style="margin:0;padding:0;width:291px;height:18px;"><img alt=""
                    src="https://apbg.net/board_images/n_low.png" style="width:291px;height:18px;" /></p>
        </td>
    </tr>
    <tr>
        <th style="width:266px;text-align:left;">Blue</th>
        <th style="width:25px;text-align:right;">84</th>
    </tr>
</table>

Since we are allowed to swap checkers and maitain the relative pip count we see that now we can elliminate more checkers and hence exploit the symmetry even further. In this case, we can ignore all but 6 checkers, and we can also quickly see that the remaining 3 checkers of each player are on consecutive opposite points, so the difference is 3 pips.

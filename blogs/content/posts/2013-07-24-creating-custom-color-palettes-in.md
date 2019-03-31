---
title: Creating Custom Color Palettes in SSRS Charts
author: Jes Borland
type: post
date: 2013-07-24T15:03:00+00:00
excerpt: SSRS has a default set of color combinations for charts. Let’s face it, they aren’t pretty.
url: /index.php/datamgmt/ssrs/creating-custom-color-palettes-in/
views:
  - 39484
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS

---
Charts in SSRS are invaluable – they allow you to view data graphically. It is much easier to tell a story with pictures than with text and numbers.

SSRS has a default set of color combinations for charts. Let’s face it, they aren’t pretty. Here’s a sample line chart. The default color palette is “BrightPastel”.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/custom color 1.png?mtime=1374677952" alt="" width="692" height="396" />
</p>

What if those colors don’t work for you? Maybe you want a lighter or darker palette. Maybe you want to incorporate corporate colors. Maybe the budding interior designer in you is emerging. Can you change them? Yes!

In SSRS, you can define custom color palettes in reports, allowing you to change the colors in a chart.

In Report Builder, click on the chart and go to the Properties window. Select the Palette property drop-down. Choose Custom.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/custom color 2.png?mtime=1374677952" alt="" width="1239" height="714" />
</p>

Then, go to the CustomPaletteColors property and select the ellipses (…).

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/custom color 3.png?mtime=1374677952" alt="" width="318" height="129" />
</p>

The ChartColor Collection Editor will appear. What you want to do here is select the colors you want to use in your charts.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/custom color 4.png?mtime=1374677953" alt="" width="501" height="358" />
</p>

On the left, under Members, click Add. Color 0 will be added to the Members list. On the right, under Color Properties, click the drop-down arrow to select a color. You can choose from the pre-defined palette, or choose More Colors  to see an expanded palette or enter an RGB value.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/custom color 5.png?mtime=1374677953" alt="" width="500" height="360" />
</p>

I’ve added four colors – Indigo, Pale Turquoise, Yellow, and Hot Pink.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/custom color 6.png?mtime=1374677953" alt="" width="499" height="360" />
</p>

When I click OK and then Preview my report, I can see the newly defined custom colors are being used!

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/custom color 7.png?mtime=1374677953" alt="" width="714" height="402" />
</p>

The only down side to this is that custom color palettes are report-specific. I know of no way to save and re-use them in other reports.
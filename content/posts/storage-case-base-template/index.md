---
title: "Storage Case Base Template"
subtitle: "A storage solution using SCAD"
image: ""
alt: ""
description: "I've been attempting to clean up my overflowing workspace, so I created the storage case base template (SCBT) in SCAD to help organize my drawer spaces."
date: 2022-08-10T12:31:18-07:00
category: [ "projects" ]
tag: ["3d printing", "openscad", "organization" ]
draft: true
---
I've been attempting to clean up my overflowing workspace, and the easiest quick fix is probably getting rid of stuff. Since I have moderate hoarding tendencies, providing better organization is my preferred solution.

Having invested heavily in [Harbor Freight portable storage cases][harborfreight.link] (similar to the [Sortimo][sortimo.link] system), I use this [customizable SCAD models][scadmodel.link] to generate some rather specialized storage bins to optimize the space in those containers.

*So I can turn this into this.*

Now I have extra storage bins from these original Harbor Freight storage cases and can repurpose them to organize some of my existing drawers. To prevent them from sliding around, I reproduced the bottom pattern of the original container to create the storage case base template (SCBT).

## Design of the SCBT

<div class="row">
    {{< figure_bqcaption src="crosshair.png" caption="Crosshair" >}}
    {{< figure_bqcaption src="scbt.png" caption="Rows and cols of crosshairs" >}}
    {{< figure_bqcaption src="dovetail.png" caption="Dovetail joint" >}}
</div>

A fundamental component of the SCBT is the crosshair. It's composed of a center node surrounded by connecting lines. Once we develop the crosshair, we use a basic nested for-loop to generate rows and columns of connected crosshairs to transform them into a base template.

Since most printer beds likely can't print the entire area required for various drawer sizes, we can join multiple SCBTs to cover a much larger footprint. To securely join these printed parts, we added a dovetail joint feature.

## Adjusting the SCAD to cover an entire drawer bottom

The most relevant variables are `num_rows` and `num_cols` to adjust the number of rows and columns of crosshairs you want to print. With your customized values, the program will output the final print dimensions of the base template and help you determine how many SCBT prints you'll need for your specific configuration.

[harborfreight.link]: https://www.harborfreight.com/20-bin-medium-portable-parts-storage-case-93928.html
[sortimo.link]: https://www.mysortimo.com/en/BOXXes-%26-Cases/T-BOXX-G/T-BOXX-G-empty/c/47221
[scadmodel.link]: https://www.thingiverse.com/thing:2836113
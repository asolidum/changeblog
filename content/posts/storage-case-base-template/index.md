---
title: "Storage Case Base Template"
subtitle: "A storage solution using SCAD"
image: "../SCBT_in_storage_bin.webp"
alt: "Harbor Freight/Stanley storage bin case with 3d printed template"
description: "I wanted to organize my drawer space and reuse my excess Harbor Freight/Stanley storage bins, so I created this SCAD model called the storage case base template (SCBT)"
date: 2022-08-10T12:31:18-07:00
category: [ "projects" ]
tag: ["3d printing", "openscad", "organization" ]
draft: false
---
I've been attempting to clean up my overflowing workspace, and the easiest quick fix is probably getting rid of stuff. Since I'm a recovering hoarder :wink:, providing better organization is my preferred solution.

<div class="row">
    {{< figure_bqcaption src="standard_storage.webp" caption="Harbor Freight original bins in case" >}}
    {{< figure_bqcaption src="specialized_bins.webp" caption="SCAD specialized bins with tools/parts" >}}
    {{< figure_bqcaption src="reduced_storage.webp" caption="SCAD specialized bins in case" >}}    
</div>

Having invested heavily in [Harbor Freight portable storage cases][harborfreight.link] (similar to the [Stanley professional organizer][stanley.link]), I use this [customizable SCAD model][scadmodel.link] to generate some rather specialized storage bins to optimize the space in those containers (8 storage bin spaces reduced to 3).

## Reusing the extra storage bins

<div class="row">
    {{< figure_bqcaption src="storage_bin_feet.webp" caption="Bottom view of storage bin" colsize=6 >}}
    {{< figure_bqcaption src="loaded_scbt.webp" caption="SCBT with bins" colsize=6 >}}
</div>
Now I have extra storage bins from these original Harbor Freight storage cases and can repurpose them to organize some of my existing drawers. As you can see from the Harbor Freight storage bins, they have feet that align with the storage case bottom depressions to prevent them from sliding around. I reproduced the bottom pattern of the original container to create the storage case base template (SCBT).

##
## Design of the SCBT

<div class="row">
    {{< figure_bqcaption src="crosshair.png" caption="Crosshair" >}}
    {{< figure_bqcaption src="scbt.png" caption="Rows and columns of crosshairs" >}}
    {{< figure_bqcaption src="dovetail.png" caption="Dovetail joint" >}}
</div>

A fundamental component of the SCBT is the crosshair. It's composed of a center node surrounded by connecting lines. Utilizing the crosshair, we employ a basic nested for-loop to generate rows and columns of connected crosshairs to transform them into a base template.

Since most printer beds likely can't print the entire area required for various drawer sizes, we can join multiple SCBTs to cover a much larger footprint. To securely join these printed parts, we added a dovetail joint feature.

## Adjusting the SCAD to cover an entire drawer bottom

The most relevant variables are `num_rows` and `num_cols` to adjust the number of rows and columns of crosshairs you want to print. With your customized values, the program will output the final print dimensions of the base template and help you determine how many SCBT prints you'll need for your specific configuration.

## Testing the Template

Here are before and after photos of one of my drawers.
<div class="row">
    {{< figure_bqcaption src="drawer_before.webp" caption="Drawer organization without SCBT" colsize=6 >}}
    {{< figure_bqcaption src="drawer_after.webp" caption="Drawer organization with SCBT" colsize=6 >}}
</div>

## Related Information

If you haven't invested heavily in the Harbor Freight/Stanley storage bins like me, you should consider some other organizational systems developed by the following amazing YouTubers:
* [Alex Chappel's Assortment System][alexassortment.link]
* [Zack Freedman's Gridfinity][zackgridfinity.link]

The latest SCAD file is available at the following git repos: [GitHub][github.link]/[GitLab][gitlab.link]  
Pre-generated STL files can be downloaded at: [Printables][printables.link]/[Thingiverse][thingiverse.link]

[harborfreight.link]: https://www.harborfreight.com/20-bin-medium-portable-parts-storage-case-93928.html
[stanley.link]: https://www.stanleytools.com/product/014725r/professional-organizer
[scadmodel.link]: https://www.thingiverse.com/thing:2836113
[github.link]: https://gitlab.com/a1s0/storage-case-base-template
[gitlab.link]: https://gitlab.com/a1s0/storage-case-base-template
[alexassortment.link]: https://www.alch.shop/shop/assortment
[zackgridfinity.link]: https://www.youtube.com/watch?v=ra_9zU-mnl8
[printables.link]: https://www.printables.com/model/280725-storage-case-base-template-scbt
[thingiverse.link]: https://www.thingiverse.com/thing:5527247

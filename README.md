
<!-- README.md is generated from README.Rmd. Please edit that file -->

# mlwdata: a repository of datasets for MLW population health in Malawi

<!-- badges: start -->

<!-- badges: end -->

mlwdata contains a set of annotated datasets that have been collected as
part of research studies conducted at the Malawi-Liverpool-Wellcome
Trust Clinical Research Programme in Blantyre, Malawi.

The main aims are to:

  - ensure consistent use of data between and within studies over time
  - facilitate sharing of data to reduce duplication of efforts

## Installation

You can install the from [GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("petermacp/mlwdata")
```

<br>

## Datasets

Currently, the following datasets are available:

**scale\_72\_clusters** <br> A `sf` MULTIPOLYGON object, containing
polygon boundaries and cluster IDs for the SCALE Study (Corbett et al),
with variables:

  - `cluster`: unique identifier for each of the 72 study clusters
  - `geometery`: a list column, containing polygons for each cluster
    boundary.

<br>

**blantyre\_clinics** <br> A `sf` POINT object, containing coordinates
for Blantyre clinics/hospitals, and clinic IDs:

  - `clinic`: unique name of each of the 18 clinics/hospitals
  - `geometry`: a list column, containing points for each
    clinic/hospital.

<br>

## Examples

The SCALE Study defined 72 geographical cluster boundaries in urban
Blantyre using GPS waypaths. These clusters can be loaded and plotted:

``` r
library(mlwdata)
library(tidyverse)

glimpse(scale_72_clusters)
#> Observations: 72
#> Variables: 2
#> $ cluster  <chr> "c1", "c2", "c3", "c4", "c5", "c6", "c7", "c8", "c9", "…
#> $ geometry <list> [35.05040, 35.05040, 35.05040, 35.05040, 35.05040, 35.…


ggplot(scale_72_clusters) +
  geom_sf() +
  geom_sf_text(aes(label=cluster), colour="blue") +
  theme_minimal() +
  labs(title="SCALE Study",
       subtitle="72 clusters",
       x="",
       y="",
       caption = "Corbett, MacPherson et al.")
```

<img src="man/figures/README-example-1.png" width="100%" />

We could add on the location of the clinics in Blantyre.

``` r

library(sf)
library(ggrepel)

qech <- blantyre_clinics %>%
  #filter to show QECH only for interest
  filter(clinic=="Queen Elizabeth hospital") %>%
  #split out the x and y coordinates to allow use of ggrepel
  st_coordinates() %>%
  as_tibble() %>%
  mutate(clinic="QECH")

ggplot() +
  geom_sf(data = scale_72_clusters) +
  geom_sf_text(data = scale_72_clusters, aes(label=cluster), colour="blue") +
  geom_sf(data=blantyre_clinics, shape=17, colour="#22211d") +
  geom_label_repel(data = qech, 
                       aes(x=X, y=Y, label=clinic), 
        fontface = "bold",
        min.segment.length = 0) +
  theme_minimal() +
  labs(title="SCALE Study",
       subtitle="72 clusters. Clinics/hospitals labelled with triangles",
       x="",
       y="",
       caption = "Corbett, MacPherson et al.") +
  coord_sf()
```

<img src="man/figures/README-example2-1.png" width="100%" />

# 3D-rendering
Adult zebrafish 3D rendering

install.packages("imager")
install.packages("rgl")

library(imager)
library(rgl)

# Function to load a z-stack from a folder
load_zstack <- function(folder) {
  file_list <- list.files(folder, pattern = "\\.tif$", full.names = TRUE)
  zstack <- lapply(file_list, load.image)
  zstack <- simplify2array(zstack)  # Convert list to array
  return(zstack)
}

# Load z-stacks from different folders
folder1 <- "/path/to/folder1"
folder2 <- "/path/to/folder2"

zstack1 <- load_zstack(folder1)
zstack2 <- load_zstack(folder2)

# Assuming zstack1 and zstack2 have the same x and y dimensions
combined_zstack <- abind::abind(zstack1, zstack2, along = 3)  # Combine along the z-axis

# Install abind if not already installed
# install.packages("abind")

# Open a 3D plot
open3d()

# Create a 3D surface for each slice in the z-stack
for (i in 1:dim(combined_zstack)[3]) {
  slice <- combined_zstack[,,i]
  surface3d(1:dim(slice)[1], 1:dim(slice)[2], matrix(i, nrow = dim(slice)[1], ncol = dim(slice)[2]), 
            col = gray(slice/max(slice)), add = TRUE)
}

# Optionally adjust the viewpoint
rgl.viewpoint(theta = 30, phi = 30, zoom = 0.8)

# Optionally add axes, labels, etc.
decorate3d()

# To interact with the plot, use rglwidget() to embed it in a Shiny app or an R Markdown document
# For static plots, use rgl.snapshot() to export as an image

# Export as an image
rgl.snapshot("3d_figure.png")

# For interactive visualization in R Markdown
library(htmlwidgets)
saveWidget(rglwidget(), "3d_figure.html")

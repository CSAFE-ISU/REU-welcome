Week 5 Updates
================
2017-06-30 12:30:00 CDT

Here's what we've done on our projects so far.

Shoeprints
----------

### James Kruse, Andrew Kimble III, Marion Gray-Lion, and Sam Rew

Project Week 1 (6/23 - 6/30) Update Slides: <https://prezi.com/p/xwjtd0_mxzvg/>

On the 22nd and 23rd of June, we scanned 8 pairs of shoes to be used later in the program. They were labeled and pushed into Git Hub for storage. In addition, we were trained to take shoe prints using dust and contact paper. They will not be extensively used later in the program, but this technique is still relevant and necessary in the field.

Over the past two days, the shoe print team has been working to understand Hu Moments and their use in shoe print analysis. In addition, we have developed a function in R studio that will allow us to take the shoe scans that were taken late last week and crop/grayscale the image for later analysis.

After being given both videos and files to review, we were tasked to put together a presentation that would both explain Hu Moments and their relevance in forensic shoe print analysis. This was then presented to our project leaders. In summary, Hu Moments are a snap shot of an image in time. If you would like to see the full presentation, please contact a member of the our CSAFE REU shoe print team.

On the 27th, we learned the program to code grayscale and crop images using RStudio after the files are downloaded from GitHub. Each of the images from the 8 sets were put through this code and saved. On the 28th, we cleaned up the code mentioned above by condensing it into a function. At this point in time, you can enter in the raw image, run the function, and end with a cropped/gray scale shoe print. Please see the finished code below for specifics.

Running these lines will pull the devtools package from github and sets up the R Studio system to retrieve the shoe files for analysis.

    install.packages("devtools")
    devtools::install_github("gbasulto/solefinder")
    library(purrr)
    library(tidyr)
    library(EBImage)
    library(solefinder)
    library(dplyr)

This line allows us to type in "filename" and a number instead of the actual name of the file. This line also organizes the tiff files alphabetically starting with "Andrew1\_left\_1.tiff".

    filenames <- list.files(pattern = "tiff")

This is the GrayCrop2 function that will alter the raw image into a grayscale, cropped, and negative image. Code chunk "img &lt;- readImage(x)" names an image "x" "img" for the next alteration step. Code chunk "img &lt;- channel(img, mode = "grey")" converts the image into grayscale. Code chunk "img &lt;- max(img) - img" converts the image into a negative image and removing the "\#\#\#" from code chunk "\#\#\#display( img\_neg )" will display this image. Code chunk" img\_crop=img\[160:1800, 200:4400\]" crops the image to remove the borders. Removing the "\#\#\#" from code chunk "\#\#\#display(img\_crop, method = "raster")" and eliminating "img\_crop" will display the final altered image.

    GrayCrop2 <- function(x){
      img <- readImage(x) 
      img <- channel(img, mode = "grey") 
      img <- max(img) - img
      ###display( img_neg )
      img_crop=img[160:1800, 200:4400]
      ###display(img_crop, method = "raster")
      img_crop
    }

This will compute the Hu moments for a single file.

    compute_moments(GrayCrop2(filenames[3]))

This is the console readout for a single image that had been subjected to the code above.

    > compute_moments(GrayCrop2(filenames[3]))
    [1] 0.969507128 0.594752685 0.103012781 0.107731366 0.011337114 0.081817373
    [7] 0.008688779 0.007222278

This is the Hu function that computes the 8 Hu moments for any file or files we specify. Code chunk "out &lt;- compute\_moments(GrayCrop2(filenames\[x\]))" names the compute moments function "out" after an image goes through the GrayCrop2 function. Code chunk "data\_frame(shoe = filenames\[x\]," pulls the data from the shoe folder that we store the prints in. Code chunk "Hu1 = out\[1\], Hu2 = out\[2\], Hu3 = out\[3\], Hu4 = out\[4\], Hu5 = out\[5\], Hu6 = out\[6\], Hu7 = out\[7\], Hu8 = out\[8\])" sets the output as the 8 Hu moments. Code chunk "d &lt;- map\_df(1:48, Hu)" saves all of the Hu moments for the 48 shoe files into a database.

    Hu<- function(x) {
      out <- compute_moments(GrayCrop2(filenames[x]))
      data_frame(shoe = filenames[x],
                 Hu1 = out[1], Hu2 = out[2], Hu3 = out[3], Hu4 = out[4],
                 Hu5 = out[5], Hu6 = out[6], Hu7 = out[7], Hu8 = out[8])
    }

    d <- map_df(1:48, Hu)

This line tidies the table and organizes the shoes by side, id, and which scan it is.

    shoeprints <- 
      d %>%
      mutate(shoe = gsub(".tiff", "", shoe)) %>%
      separate(shoe, c("id", "side", "rep"), sep = "_")

This is the head of the shoeprints data set after being tidyed.

    > head(shoeprints)
           id  side rep       Hu1       Hu2        Hu3        Hu4         Hu5        Hu6
    1 Andrew1  left   1 0.9839185 0.6127382 0.10087957 0.10479915 0.010775211 0.08025148
    2 Andrew1  left   2 0.9922167 0.6454597 0.04756689 0.04170379 0.001857218 0.03235382
    3 Andrew1  left   3 0.9695071 0.5947527 0.10301278 0.10773137 0.011337114 0.08181737
    4 Andrew1 right   1 0.9896718 0.6210294 0.07609011 0.07631862 0.005774333 0.05894313
    5 Andrew1 right   2 0.9063717 0.5127025 0.04711124 0.04815755 0.002274318 0.03384983
    6 Andrew1 right   3 0.9273094 0.5297408 0.07389964 0.07629144 0.005710214 0.05422747
               Hu7          Hu8
    1  0.008300279  0.008504707
    2  0.001537663  0.004353666
    3  0.008688779  0.007222278
    4 -0.004813498 -0.005977293
    5 -0.001554405 -0.003287214
    6 -0.004232865 -0.005972354

For images and a presentation on the work from the past 2 weeks, please see the Prezi linked in the url below:

<https://prezi.com/p/xwjtd0_mxzvg/>

After presenting on Hu Moments on the 29th, we continued to develop the code that is present in the attached presentation. Our end product for the day was the breakdown of each file and the cleaning up of the data.

On Friday the 30th, we put together the week summary presentation, listened to a presentation on experiment design, and continued to develop figures using the data we have collected.

The past few days have been both fascinating and challenging. We would like to thank our REU leaders and project leaders for their patience and assistance through it all.

-CSAFE REU Shoe Project Team

Steganography
-------------

### Macy Neblett, Maddie Quistgaard, Anna Steffensmeier, Kahlil Sample, Amanda Rae

The first week of the project, we each download between 3 to 7 steganography apps on our phones. Because part of the group had Android phones and the other had Apple, we had to download different apps (the apps not cross compatible). After creating a stego image using each of the apps, we took note of the file type and size and compared them to the cover image and noted any differences.

The second week, we learned how to create matrices, histograms, and how to identify and alter the least significant bitplane (LSB).We used a function to create random messages that we used as the payload and to embed the message into the cover images. Each stego image has a unique randomly generated message. We created 50 stego images with five different embedding rates. We also created histograms for each stego image.

![Stego embedding function](Matlab.png) This image shows the function used to create the random message and to embed the message into a cover image.

![Histogram code](CS2graphcode.png) This image shows the code used to create the histograms of the stego images.

![Stego Histogram](CS2graph.png) This image displays the histogram generated by the code in the previous image.

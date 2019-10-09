# Convert MOV to animated GIF 
(from https://gist.github.com/dergachev/4627207)

```
ffmpeg -i in.mov -s 600x400 -pix_fmt rgb24 -r 10 -f gif - | gifsicle --optimize=3 --delay=3 > out.gif
```

Notes on the arguments:

`-r 10` tells ffmpeg to reduce the frame rate from 25 fps to 10

`-s 600x400` tells ffmpeg the max-width and max-height

`--delay=3` tells gifsicle to delay 30ms between each gif

`--optimize=3` requests that gifsicle use the slowest/most file-size optimization

`-l` tells gifsicle to loop forever


Credits:
M. De Domenico, D. Brockmann, C. Camargo, C. Gershenson, D. Goldsmith, S. Jeschonnek, L. Kay, S. Nichele, J.R. Nicolás, T. Schmickl, M. Stella, J. Brandoff, A.J. Martínez Salinas, H. Sayama. Complexity Explained (2019). DOI 10.17605/OSF.IO/TQGNW

Links:
https://complexityexplained.github.io/
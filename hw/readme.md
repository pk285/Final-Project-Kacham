# Prahasi Kacham HW WSS 2017 Web Development
### Websites are a great to way to display your brand, promote your business or create a portfolio. With websites, the possibilities are endless.

## History
#### Websites over time have transformed from symbols in the terminal to viewing a website in a browser. After that, there was the introduction of JavaScript, Flash and CSS which are all assets to creating a website.Then came responsive web design, grid layout and frameworks. The future: that's up to you.

## Why is Web Development important?
#### Web development helps display quality information that has a great user interface and has a large influence on whoever visits the website. This type of development has grown tremendously over the past few years and with the introduction of web libraries, anything is possible.

## Figuring out what to add to the webpage
#### There are many different options as to what you can add on a webpage. Here are some examples of what you can add!

```Mathematica
(* Basic text with some styling *)
Style["Hello, I am a programmer.", FontColor -> Red, FontSize -> 16]
(* Interpret function as HTML *)
ExportString[
 Style["Hello, I am a programmer.", FontColor -> Red, 
  FontSize -> 16], "HTMLFragment"]
```

```Mathematica
(* Highlight text *)
Style["This was a project I did for my freshman year. Check it out below!", Background -> Pink]
(* Interpret function as HTML *)
ExportString[
 Style["This was a project I did for my freshman year. Check it out below!", Background -> Pink], "HTMLFragment"]
```

```Mathematica
(* Display an area on a website *)
FormFunction[{"city" -> "City"}, GeoGraphics[#city] &, "PNG"]
(* Interpret function as HTML *)
ExportString[FormFunction[{"city" -> "City"}, GeoGraphics[#city] &, "PNG"], "HTMLFragment"]
```

```Mathematica
(* Create a graphics shape  *)
Graphics[Disk[]]
(* Interpret function as XML *)
ExportString[Graphics[Disk[]], "SVG"]
```


```Mathematica
(* Display images *)
Import["https://www.google.com/search?q=dog", "Images"]
```

```Mathematica
(* Deploy the FormFunction to the web *)
CloudDeploy[FormFunction[{"city" -> "City"}, GeoGraphics[#city] &, "PNG"]]
```

## Creating a simple web app that tells you the population of a country
```Mathematica
(* Create a functional web app *)
CloudDeploy[
  FormFunction[{{"x", "Find the population of this country:"} -> 
    "Country"}, CountryData[#x, "Population"] &],Permissions -> "Public"]
(* Interpret function as HTML *)
ExportString[
 FormFunction[{{"x", "Find the population of this country:"} -> 
    "Country"}, CountryData[#x, "Population"] &], "HTMLFragment"]
```

## You can change the layout of a form on a web app

``` Mathematica
(* Deploy a blue form *)
CloudDeploy[
 FormFunction[{"city" -> "City"}, GeoGraphics[#city] &, "PNG", 
  PageTheme -> "Black"]]
```
## Create a simple website that lets you upload a picture and detects the number of edges 

```Mathematica
(* Cloud Deploy an app that detect edges of an image *)
CloudDeploy[FormPage[{"image" -> "Image"}, EdgeDetect [#image] &], 
 Permissions -> "Public"]
```

## Advanced Features
#### Web API's

```Mathematica
(* Create code for calling an API from C# *)
EmbedCode[APIFunction[{"x" -> "Integer"}, #x^4 &], "C#"]
```

#### Embed 
```Mathematica
(* Create embed code to help embed in other languages *)
EmbedCode[CloudDeploy["I am a programmer"]]
```

### Further Explorations
#### Mobile Apps

### Authorship information
#### Prahasi Kacham
#### 6/23/17
#### pk285@mynsu.nova.edu

# sass-recursive-map-merge

A lightweight Sass function that merges multidimensional maps recursively.

## Motivation

[Sass](http://sass-lang.com/) introduced the [map data type](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#Maps) in [version 3.3.0](https://github.com/sass/sass/releases/tag/3.3.0) and _it is awesome_. Maps come with a [wide variety of functions](http://sass-lang.com/documentation/Sass/Script/Functions.html#map-functions) and they help developers store data in a more maintainable and perhaps more meaningful way as opposed to simple variables.

I find though that the built-in functions fall short when working with multidimensional maps. Consider you have the following maps and you want to merge them.

```scss
$colors1: (
    primary: (
        light: #f66124,
        dark: #ad3707
    )
);

$colors2: (
    primary: (
        base: #de4709
    )
);
```

Using [`map-merge()`](http://sass-lang.com/documentation/Sass/Script/Functions.html#map_merge-instance_method), the result depends on the order of the parameters. According to the specification:

>Keys in `$map2` will take precedence over keys in `$map1`.

`$map1` is the first and `$map2` is the second parameter. This means that we can merge our previous maps in two different ways using `map-merge()`.

```scss
$colors1: (
    primary: (
        light: #f66124,
        dark: #ad3707
    )
);

$colors2: (
    primary: (
        base: #de4709
    )
);

$mergedcolors1: map-merge($colors1, $colors2);

//$mergedcolors1: (
//    primary: (
//        base: #de4709
//    )
//);

$mergedcolors2: map-merge($colors2, $colors1);

//$mergedcolors2: (
//    primary: (
//        light: #f66124,
//        dark: #ad3707
//    )
//);
```

Either way, we get back one of the original maps, which is the expected behavior. `map-merge()` goes through the keys of each map, checks whether they exist in both maps or not. Unique keys are added to the returned map, and in cases where the key exists in both maps, the key from the second map is added to the returned map.

However, I wanted to get this result:

```scss
$colors1: (
    primary: (
        light: #f66124,
        dark: #ad3707
    )
);

$colors2: (
    primary: (
        base: #de4709
    )
);

$colors: recursive-map-merge($colors1, $colors2);

//$colors: (
//    primary: (
//        light: #f66124,
//        dark: #ad3707,
//        base: #de4709
//    )
//);
```

I created `recursive-map-merge()` to merge multidimensional maps in a way that map data types contained within the merging maps also get merged.

## Getting Started

### Prerequisites

This Sass function runs on Sass version 3.3.0 or higher. It has no dependencies and can be used in any kind of Sass code.

The code is currently published on [npm](https://www.npmjs.com/), a package manager for [Node.js](https://nodejs.org/en/), so make sure both of them are installed on your computer for easy use. You can download an installer for your operating system on the [Downloads page of Node.js](https://nodejs.org/en/download/).

### Installing

#### Via [npm](https://www.npmjs.com/)

In order to get a copy, all you need to do is install the package in your own [npm](https://www.npmjs.com/) project.

```
npm install sass-recursive-map-merge
```

After this, the source will be downloaded into the project's `node_modules` folder. From there, you can simply include the code in your own codebase using Sass' `@import` directive.

If your project depends on Sass to create CSS code, it is recommended to save this package in your project's `package.json` file.

```
npm install sass-recursive-map-merge --save-dev
```

However, if you happen to use this code in a project which is for instance a Sass framework and you want to include this work in that framework, save the package as a dependency.

```
npm install sass-recursive-map-merge --save
```

#### Copying the source

Although I personally do not recommend doing this, but you can simply download the source file from this repository and use it in your project that way. Note however, that [npm](https://www.npmjs.com/) makes updating packages a breeze and you should probably use it anyway.

## Usage

### Parameters

`recursive-map-merge()` expects two parameters:

* `$map1` (map)
* `$map2` (map)

### Return value

* `$result` (map)

### Raises

* Warning, if the type of one of the parameters is not map.

### Example

This function works best in a scenario where you have some sort of base variables and want to extend that collection in different cases. If you maintain style sheets of multiple sites that are similar in how they look, but have their own brand as well, and you want to share those base variables between multiple style sheets and want to extend them with the specific ones, `recursive-map-merge()` can prove to be very useful.

In the following scenario, we have three different shades of neutral colors and a success color in our base colors and we want to extend the neutral colors with a new shade for the specific colors and also add the primary colors to the final colors.

```scss
$base-colors: (
    neutral: (
        light: white,
        base: gray,
        dark: black
    ),
    success: (
        base: #007b39
    )
);

$specific-colors: (
    neutral: (
        mid-light: lightgray
    ),
    primary: (
        light: lightblue,
        base: blue,
        dark: darkblue
    )
);

$colors: recursive-map-merge($base-colors, $specific-colors);

//$colors: (
//    neutral: (
//        light: white,
//        mid-light: lightgray
//        base: gray,
//        dark: black
//    ),
//    success: (
//        base: #007b39
//    ),
//    primary: (
//        light: lightblue,
//        base: blue,
//        dark: darkblue
//    )
//);
```

`recursive-map-merge()` is capable of dealing with maps that are more than two dimensional. The following example deals with sizes in a similar scenario than with colors before, but with three dimensional maps.


```scss
$base-sizes: (
    gutter: (
        mobile: (
            small: 10px,
            base: 20px,
            large: 30px
        ),
        desktop: (
            small: 15px,
            base: 30px,
            large: 45px
        )
    )
);

$specific-sizes: (
    gutter: (
        mobile: (
            x-large: 40px
        ),
        tablet: (
            small: 12px,
            base: 24px,
            large: 36px,
            x-large: 48px
        ),
        desktop: (
            x-large: 60px
        )
    )
);

$sizes: recursive-map-merge($base-sizes, $specific-sizes);

//$sizes: (
//    gutter: (
//        mobile: (
//            small: 10px,
//            base: 20px,
//            large: 30px,
//            x-large: 40px
//        ),
//        tablet: (
//            small: 12px,
//            base: 24px,
//            large: 36px,
//            x-large: 48px
//        ),
//        desktop: (
//            small: 15px,
//            base: 30px,
//            large: 45px,
//            x-large: 60px
//        )
//    )
//);
```

## Contributing

Questions and feedback are welcome. If you have anything to add, found a bug or a spelling error, or just want to start a discussion about this project, feel free to [open an issue on GitHub](https://github.com/pentzzsolt/sass-recursive-map-merge/issues).

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions available, see the [tags on this repository](https://github.com/pentzzsolt/sass-recursive-map-merge/tags). 

## Authors

* **Zsolt Pentz** - *Initial work* - [pentzzsolt](https://github.com/pentzzsolt)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details

## Acknowledgments

* Big thank you to [PurpleBooth](https://github.com/PurpleBooth) for [this lovely README template](https://gist.github.com/PurpleBooth/109311bb0361f32d87a2)!

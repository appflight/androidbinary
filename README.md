androidbinary
=====

[![Build Status](https://github.com/appflight/androidbinary/workflows/Test/badge.svg)](https://github.com/appflight/androidbinary/actions)
[![GoDoc](https://godoc.org/github.com/appflight/androidbinary?status.svg)](https://godoc.org/github.com/appflight/androidbinary)

Android binary file parser

## High Level API

### Parse APK files

``` go
package main

import (
	"fmt"
	"github.com/appflight/androidbinary"
	"github.com/appflight/androidbinary/apk"
)

func main() {
	pkg, _ := apk.OpenFile("your-android-app.apk")
	defer pkg.Close()

	icon, _ := pkg.Icon(nil) // returns the icon of APK as image.Image
	pkgName := pkg.PackageName() // returns the package name

	resConfigEN := &androidbinary.ResTableConfig{
		Language: [2]uint8{uint8('e'), uint8('n')},
	}
	appLabel, _ := pkg.Label(resConfigEN) // get app label for en translation
	
	fmt.Println(icon, pkgName, appLabel)
}
```

## Low Level API

### Parse XML binary

``` go
package main

import (
	"encoding/xml"
	"fmt"
	"github.com/appflight/androidbinary"
	"github.com/appflight/androidbinary/apk"
	"io/ioutil"
	"os"
)

func main() {
	f, _ := os.Open("path to AndroidManifest.xml")
	xmlFile, _ := androidbinary.NewXMLFile(f)
	reader := xmlFile.Reader()

	// read XML from reader
	var manifest apk.Manifest
	data, _ := ioutil.ReadAll(reader)
	xml.Unmarshal(data, &manifest)
	
	fmt.Println(manifest)
}
```

### Parse Resource files

``` go
package main

import (
	"fmt"
	"github.com/appflight/androidbinary"
	"os"
)

func main() {
	f, _ := os.Open("path to resources.arsc")
	rsc, _ := androidbinary.NewTableFile(f)
	resource, _ := rsc.GetResource(androidbinary.ResID(0xCAFEBABE), nil)
	fmt.Println(resource)
}
```

## License

This software is released under the MIT License, see LICENSE.

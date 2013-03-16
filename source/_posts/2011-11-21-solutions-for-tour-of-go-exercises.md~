---
title: Solutions for Tour of Go exercises
author: sw
layout: post
permalink: /2011/11/solutions-for-tour-of-go-exercises/
categories:
  - Uncategorized
tags:
  - go
  - golang
---
# 

I am learning [Go][1] and the [Tour of Go][2], an interactive tutorial, is a good resource to start with. At the end of each section is a series of exercises for the reader to complete.

 [1]: http://golang.org/
 [2]: http://tour.golang.org/

Unfortunately, there are no example solutions for those exercises, so its hard to get a hint if you are stuck. Therefore I post my solutions here. I do not think they are the best way to solve the problems in Go (as I said, I am learning too) but code is compiling and it is doing what it should. You are welcome to comment with your solutions and code improvements.

*   [Exercise: Loops and Functions][3] 
        package main
        
        import (
        	"fmt"
        	"math"
        )
        
        func Sqrt(x float64) float64 {
            var z, d float64 = x, 1
            for d > 1e-15 {
                z0 := z
                z = z-(z*z-x)/(2*z)
                d = z-z0
                if d < 0 {
                    d = -d
                }
            }
            return z
        }
        
        func main() {
        	fmt.Println(Sqrt(2))
        	fmt.Println(math.Sqrt(2))
        }
        

*   [Exercise: Maps][4] 
        package main
        
        import (
        	"strings"
        	"tour/wc"
        )
        
        func WordCount(s string) map[string]int {
        	m:=make(map[string]int)
        	for _, w:=range strings.Fields(s) {
        		m[w]++
        	}
        	return m
        }
        
        func main() {
        	wc.Test(WordCount)
        }
        

*   [Exercise: Slices][5] 
        package main
        
        import "tour/pic"
        
        func Pic(dx, dy int) [][]uint8 {
            mat := make([][]uint8, dx) 
            for i := range mat { 
                mat[i] = make([]uint8, dy)
        	for j := range mat[i] {
        	    mat[i][j]=uint8(i^j)
        	}
            }
            return mat
        }
        
        func main() {
        	pic.Show(Pic)
        }
        

*   [Exercise: Fibonacci closure][6] 
        package main
        
        import "fmt"
        
        // fibonacci is a function that returns
        // a function that returns an int.
        func fibonacci() func() int {
        	var a, b, count int = 0, 1, 0
        	return func() int {
        		switch count {
        		case 0:  count++
        			 return a 
        		case 1:  count++
        			 return b
        		}
        		sum:=a b
        		a=b
        		b=sum
        		return sum
        	}
        }
        
        func main() {
        	f := fibonacci()
        	for i := 0; i < 10; i   {
        		fmt.Println(f())
        	}
        }
        

*   [Advanced Exercise: Complex cube roots][7] 
        package main
        
        import (
        	"fmt"
        	"math"
        	"cmath"
        )
        
        func Cbrt(x complex128) complex128 {
            var z complex128 = x
            for i:=0; i 1e-15 {
                z0 := z
                z = z-(z*z-f)/(2*z)
                d = z-z0
                if d < 0 {
                    d = -d
                }
            }
            return z, nil
        }
        
        func main() {
        	if value, err := Sqrt(-2.0); err != nil {
        		fmt.Println(err)
        	} else {
        		fmt.Println(value)
        	}
        }
        

*   [Exercise: Images][9] 
        package main
        
        import (
        	"image"
        	"tour/pic"
        )
        
        type Image struct {}
        
        func (m *Image) ColorModel() image.ColorModel {
            return image.RGBAColorModel
        }
        
        func (m *Image) Bounds() image.Rectangle {
            return image.Rect(0,0,256,256)
        }
        
        func (m *Image) At(x, y int) image.Color {
            v:=uint8(x^y)
            return image.RGBAColor{v, v, 255, 255}
        }
        
        func main() {
        	m := &Image{}
        	pic.ShowImage(m)
        }
        

*   [Exercise: Rot13 Reader][10] 
        package main
        
        import (
        	"io"
        	"os"
        	"strings"
        )
        
        type rot13Reader struct {
        	r io.Reader
        }
        
        func rot13(b byte) byte { 
            switch { 
                case 'A' <= b && b <= 'M': 
                        b = (b - 'A')+'N' 
                case 'N' <= b && b <= 'Z': 
                        b = (b - 'N')+'A' 
                case 'a' <= b && b <= 'm': 
                        b = (b - 'a')+'n' 
                case 'n' <= b && b <= 'z': 
                        b = (b - 'n')+'a' 
            } 
            return b 
        }

        func (b *rot13Reader) Read(p []byte) (n int, err os.Error) {
	        n, err = b.r.Read(p)
	        for i:=range(p[:n]) {
		        p[i]=rot13(p[i])
	        }
	        return
        }

        func main() {
	        s := strings.NewReader(
		        "Lbh penpxrq gur pbqr!")
	        r := rot13Reader{s}
	        io.Copy(os.Stdout, &r)
        }


*   [Exercise: Equivalent Binary Trees][11] 
        package main

        import (
	        "fmt"
	        "tour/tree"
        )

        // Walk walks the tree t sending all values
        // from the tree to the channel ch.
        func Walk(t *tree.Tree, ch chan int) {
	        if t.Left != nil {
		        Walk(t.Left, ch)
	        }
	        ch<-t.Value
	        if t.Right != nil {
		        Walk(t.Right, ch)
	        }
        }

        // Same determines whether the trees
        // t1 and t2 contain the same values.
        func Same(t1, t2 *tree.Tree) bool {
	        ch1:=make(chan int)
	        ch2:=make(chan int)
	        go Walk(t1, ch1)
	        go Walk(t2, ch2)
	        for i:=0; i<10; i++ {
		        if <-ch1 != <-ch2 {
			        return false
		        }
	        }
	        return true
        }	

        func main() {
	        ch := make(chan int)
	        go Walk(tree.New(1), ch)
	
	        for i:=0; i<10; i++ {
		        fmt.Println(<-ch)
	        }
	
	        fmt.Println("Equivalent Binary Trees?",
		        Same(tree.New(1), tree.New(1)))
	
	        fmt.Println("Equivalent Binary Trees?",
		        Same(tree.New(1), tree.New(2)))
        }


*   [Exercise: Web Crawler][12] 
        package main

        import (
	        "os"
	        "fmt"
        )

        type Fetcher interface {
	        // Fetch returns the body of URL and
	        // a slice of URLs found on that page.
	        Fetch(url string) (body string, urls []string, err os.Error)
        }

        // Crawl uses fetcher to recursively crawl
        // pages starting with url, to a maximum of depth.
        func Crawl(url string, depth int, fetcher Fetcher) {
	        type target struct{
		        urls  []string
		        depth int
	        }
	        more := make(chan target)

	        fetchPage := func(url string, depth int) {
		        body, urls, err := fetcher.Fetch(url)
		        if err != nil {
			        fmt.Println(err)
		        } else {
			        fmt.Printf("found: %s %q\n", url, body)
		        }
		        more <- target{urls, depth+1}
	        }

	        go fetchPage(url, 0)

	        visited := map[string]bool{url:true}
	        for counter := 1; counter > 0; {
		        next := <-more
		        counter--
		        if next.depth > depth {
			        continue
		        }
		        for _, url := range next.urls {
			        if _, seen := visited[url]; seen {
				        continue
			        }
			        visited[url] = true
			        counter++
			        go fetchPage(url, next.depth)
		        }
	        }
        }

        func main() {
	        Crawl("http://golang.org/", 4, fetcher)
        }


        // fakeFetcher is Fetcher that returns canned results.
        type fakeFetcher map[string]*fakeResult

        type fakeResult struct {
	        body string
	        urls     []string
        }

        func (f *fakeFetcher) Fetch(url string) (string, []string, os.Error) {
	        if res, ok := (*f)[url]; ok {
		        return res.body, res.urls, nil
	        }
	        return "", nil, fmt.Errorf("not found: %s", url)
        }

        // fetcher is a populated fakeFetcher.
        var fetcher = &fakeFetcher{
	        "http://golang.org/": &fakeResult{
		        "The Go Programming Language",
		        []string{
			        "http://golang.org/pkg/",
			        "http://golang.org/cmd/",
		        },
	        },
	        "http://golang.org/pkg/": &fakeResult{
		        "Packages",
		        []string{
			        "http://golang.org/",
			        "http://golang.org/cmd/",
			        "http://golang.org/pkg/fmt/",
			        "http://golang.org/pkg/os/",
		        },
	        },
	        "http://golang.org/pkg/fmt/": &fakeResult{
		        "Package fmt",
		        []string{
			        "http://golang.org/",
			        "http://golang.org/pkg/",
		        },
	        },
	        "http://golang.org/pkg/os/": &fakeResult{
		        "Package os",
		        []string{
			        "http://golang.org/",
			        "http://golang.org/pkg/",
		        },
	        },
        }

 [3]: http://tour.golang.org/#44
 [4]: http://tour.golang.org/#45
 [5]: http://tour.golang.org/#46
 [6]: http://tour.golang.org/#47
 [7]: http://tour.golang.org/#48
 [8]: http://tour.golang.org/#58
 [9]: http://tour.golang.org/#59
 [10]: http://tour.golang.org/#60
 [11]: http://tour.golang.org/#69
 [12]: http://tour.golang.org/#70

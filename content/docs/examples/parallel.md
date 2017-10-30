---
title: "parallel"
categories: [examples]
menu:
  docs:
    parent: "examples"
---

```go
package main

import (
	"fmt"

	"github.com/asciimoo/colly"
)

func main() {
	// Instantiate default collector
	c := colly.NewCollector()

	// Limit the maximum parallelism to 5
	// This is necessary if the goroutines are dynamically
	// created to control the limit of simultaneous requests.
	//
	// Parallelism can be controlled also by spawning fixed
	// number of go routines.
	c.Limit(&colly.LimitRule{DomainGlob: "*", Parallelism: 5})

	// MaxDepth is 2, so only the links on the scraped page
	// and links on those pages are visited
	c.MaxDepth = 2

	// On every a element which has href attribute call callback
	c.OnHTML("a[href]", func(e *colly.HTMLElement) {
		link := e.Attr("href")
		// Print link
		fmt.Println(link)
		// Visit link found on page on a new thread
		go e.Request.Visit(link)
	})

	// Start scraping on https://en.wikipedia.org
	c.Visit("https://en.wikipedia.org/")
	// Wait until threads are finished
	c.Wait()
}
```
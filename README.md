# go-aws-news

Fetch what's new from AWS and send out notifications on social sites.

<p align="center"><img src="https://i.imgur.com/U7zlAGc.png"/></p>

[![Build Status](https://travis-ci.org/circa10a/go-aws-news.svg?branch=master)](https://travis-ci.org/circa10a/go-aws-news)
[![Go Doc](https://godoc.org/github.com/circa10a/go-aws-news?status.svg)](http://godoc.org/github.com/circa10a/go-aws-news)
[![Go Report Card](https://goreportcard.com/badge/github.com/circa10a/go-aws-news)](https://goreportcard.com/report/github.com/circa10a/go-aws-news)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/circa10a/go-aws-news?style=plastic)

[go-aws-news](#go-aws-news)
  * [App Install](#app-install)
      - [Notification Providers](#notification-providers)
      - [Install Options](#install-options)
  * [Module Install](#module-install)
  * [Module Usage](#module-usage)
      - [Get Today's news](#get-todays-news)
      - [Get Yesterday's news](#get-yesterdays-news)
      - [Get all news for the month](#get-all-news-for-the-month)
      - [Gets from a previous month](#gets-from-a-previous-month)
      - [Print out announcements](#print-out-announcements)
      - [Loop over news data](#loop-over-news-data)
      - [Get news as JSON](#get-news-as-json)
      - [Get news as HTML](#get-news-as-html)
      - [Get news about a specific product](#get-news-about-a-specific-product)
  * [Development](#development)
    + [Test](#test)

## App Install

`go-aws-news` can be executed as an application that sends out notifications to social sites like
[Discord]. To configure providers, modify the [config.yaml](config.yaml) file to __enable__ a
provider.

`go-aws-news` is designed to be run on a schedule, once a day (displaying the previous day's AWS
News). See the [install options](#install-options) for examples on how to install and run.

### Notification Providers

Currently supported providers:

- [Discord](providers/discord/README.md)
- [Yammer](providers/yammer/README.md)

### Install Options

<details>
<summary>Run via crontab</summary>

The simplest way to run `go-aws-news` is via crontab.

Type `crontab -e` on Mac or Linux and add a line:

```shell
0 2 * * * /path/to/go-aws-news-binary
```

>The above example will execute `go-aws-news` at `2AM UTC` (8AM CST) each day.

</details>

<details>
<summary>Run via Kubernetes CronJob</summary>
...coming soon...
</details>

## Module Install

`go-aws-news` can be installed as a module for use in other Go applications:

```shell
go get -u "github.com/circa10a/go-aws-news/news"
```

## Module Usage

Methods return a slice of structs which include the announcement title, a link, and the date it was posted as well an error. This allows you to manipulate the data in whichever way you please, or simply use `Print()` to print a nice ASCII table to the console.

#### Get Today's news

```go
package main

import "github.com/circa10a/go-aws-news/news"

func main() { 
	news, err := awsnews.Today()
	if err != nil {
		// Handle error
	}
	news.Print()
}
```

#### Get Yesterday's news

```go
news, _ := awsnews.Yesterday()
```

#### Get all news for the month

```go
news, _ := awsnews.ThisMonth()
```

#### Gets from a previous month

```go
// Custom timeframe(June 2019)
news, err := awsnews.Fetch(2019, 06)
```

#### Print out announcements

```go
news, _ := awsnews.ThisMonth()
news.Print()
// Console output
// +--------------------------------+--------------+
// |          ANNOUNCEMENT          |     DATE     |
// +--------------------------------+--------------+
// | Amazon Cognito now supports    | Jan 10, 2020 |
// | CloudWatch Usage Metrics       |              |
// +--------------------------------+--------------+
// | Introducing Workload Shares in | Jan 10, 2020 |
// | AWS Well-Architected Tool      |              |
// +--------------------------------+--------------+
//
```

#### Loop over news data

```go
// Loop slice of stucts of announcements
// For your own data manipulation
news, _ := awsnews.Fetch(time.Now().Year(), int(time.Now().Month()))
for _, v := range news {
	fmt.Printf("Title: %v\n", v.Title)
	fmt.Printf("Link: %v\n", v.Link)
	fmt.Printf("Date: %v\n", v.PostDate)
}
```

#### Get news as JSON

```go
news, _ := awsnews.ThisMonth()
json, jsonErr := news.JSON()
if jsonErr != nil {
	log.Fatal(err)
}
fmt.Println(string(json))
```

#### Get news as HTML

```go
news, _ := awsnews.ThisMonth()
html := news.HTML()
fmt.Println(html)
```

#### Get news about a specific product

```go
news, err := awsnews.Fetch(2019, 12)
if err != nil {
	fmt.Println(err)
} else {
	news.Filter([]string{"EKS", "ECS"}).Print()
}
```

## Development

### Test

```shell
# Unit/Integration tests
make
# Get code coverage
make coverage
```

[discord]:https://discordapp.com/

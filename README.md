# Dovetail

Friendly HTML Components for Go, inspired by SwiftUI, Elm, React.

- Produce ARIA-compatible HTML markup easily
- Create components using functions

```go
Render(
  w,
  Div(
    Header(
      Nav(
        AriaLabel("Primary"),
        List(
          Link("/", Text("Home")),
          Link("/about", Text("About")),
          Link("/pricing", Text("Pricing")),
          Link("/sign-in", Text("Sign In")),
          Link("/join", Text("Join")),
        ),
      ),
    ),
    Main(
      Article(
        H(1, Text("Welcome")),
        Markdown(content),
      ),
    ),
  )
)
```

## Define components

Simple components can be defined using functions. These functions can take any number of arguments, and return a composite of other components.

```go
func PrimaryButton(text string) View {
	return Button(Text(text)).Class("btn btn-primary")
}
```

More complex components can be defined using structs that hold state:

```go
type PrimaryNavView struct {
  CurrentURL string
}

func PrimaryNav(currentURL string) HTMLText {
	return PrimaryNavView{CurrentURL: currentURL}
}

func (view PrimaryNavView) item(to string, text string) HTMLView {
  Link(to, Text(text), When(view.CurrentURL == to, Attr("aria-current", "page")))
}

func (view PrimaryNavView) Body() HTMLView {
	return Nav(
    List(
      view.item("/", "Home"),
      view.item("/about", "About"),
      view.item("/pricing", "Pricing"),
      view.item("/sign-in", "Sign In"),
      view.item("/join", "Join"),
    ),
  )
}
```

## Tailwind

Dovetail has native support for [Tailwind](https://tailwindcss.com/) classes

```go
Div().AddClasses(Tailwind(Pt8, Pb8, Text2XL, FontBold))
```

## Performance

While not trying to be the fastest HTML producer possible, Dovetail aims to be faster than `html/template` to parse and execute.

Run `make test_bench` to see how Dovetail performs to produce a variety of HTML components:

```
go test -p 1 -timeout 30s -bench="Bench" -benchmem -v -run "Bench" ./...
goos: darwin
goarch: amd64
pkg: github.com/RoyalIcing/dovetail
BenchmarkTailwindJustDiv-8                  	 4758601	       247 ns/op	     272 B/op	       2 allocs/op
BenchmarkTailwind0Classes-8                 	 4620313	       249 ns/op	     272 B/op	       2 allocs/op
BenchmarkTailwind2Classes-8                 	 2074034	       580 ns/op	     400 B/op	       6 allocs/op
BenchmarkTailwind4Classes-8                 	 1713938	       694 ns/op	     464 B/op	       7 allocs/op
BenchmarkTailwind8Classes-8                 	 1349778	       887 ns/op	     640 B/op	       8 allocs/op
BenchmarkTailwindAddClasses2Classes-8       	 1990402	       601 ns/op	     416 B/op	       6 allocs/op
BenchmarkTailwindAddClasses4Classes-8       	 1771840	       677 ns/op	     480 B/op	       6 allocs/op
BenchmarkTailwindAddClasses8Classes-8       	 1504893	       783 ns/op	     656 B/op	       6 allocs/op
BenchmarkTailwindDivWithClasses8Classes-8   	 1757682	       729 ns/op	     528 B/op	       5 allocs/op
BenchmarkTailwindChangeClasses2Classes-8    	 1797513	       643 ns/op	     464 B/op	       8 allocs/op
BenchmarkTailwindChangeClasses4Classes-8    	 1533936	       790 ns/op	     560 B/op	       9 allocs/op
BenchmarkTailwindChangeClasses8Classes-8    	 1234028	       970 ns/op	     800 B/op	      10 allocs/op
BenchmarkText-8                             	 7639869	       154 ns/op	     128 B/op	       2 allocs/op
BenchmarkHeader-8                           	 4320638	       251 ns/op	     272 B/op	       2 allocs/op
BenchmarkDiv-8                              	 4858587	       242 ns/op	     272 B/op	       2 allocs/op
BenchmarkDivWithClasses1-8                  	 2697412	       445 ns/op	     336 B/op	       4 allocs/op
BenchmarkDivWithClasses2Together-8          	 2296304	       519 ns/op	     368 B/op	       5 allocs/op
BenchmarkDivWithClasses2-8                  	 2066386	       576 ns/op	     384 B/op	       6 allocs/op
BenchmarkDivWithChildClassNames-8           	 1734169	       672 ns/op	     448 B/op	       9 allocs/op
BenchmarkButton-8                           	 2446404	       478 ns/op	     352 B/op	       5 allocs/op
BenchmarkButtonSubmit-8                     	 2451301	       480 ns/op	     352 B/op	       5 allocs/op
BenchmarkH1-8                               	 2267946	       521 ns/op	     448 B/op	       6 allocs/op
PASS
ok  	github.com/RoyalIcing/dovetail	39.216s
```

## Why?

Because I want to create server-rendered web apps that I can host cheaply on GCP App Engine Standard.

I want something that is both user friendly (quick to load, accessible) while also being reasonably developer friendly.

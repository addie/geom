# Development

This document describes how to create test cases for Makevalid

# Prerequisites

## Enabling debuging

Each package should have a file called `debug.go`, in this file there
should be a constant bool called debug set to false.

```go

const debug = false

```

By setting `debug` to true enables additional debug logging.

In your code if you have some debugging code, please surround it with the following 
if statement:

```go
if debug {
   …
}
```

## Testing

We use table driven tests; that are laid out in a very specific way.

```go
// TestNameOfFunc is the test function required by go's testing framework
func TestNameOfFunc(t *testing.T) {
	// tcase is a struct that describes the test case.
	// containing all values need to test the function
	type tcase struct {
		...
	}

	// fn is the testing function that takes a test case
	fn :=  func(tc tcase) func(*testing.T){
		return func(t *testing.T){
			// the testing codes goes there,
			// you have access to everything your need from the tc
			// and t.
			...
		}
	}

	// tests is a list of the various test cases to test for. The name
	// of the test case is important, as it allows one to select only that
	// test when running tests; using `go test -run 'TestNameOfFunc/regex_of_name_of_testcase'`
	tests := map[string]tcase{
		...
	}

	// scaffolding code to run the tests
	for name, tc := range tests {
		t.Run(name,fn(tc))
	}
}
```

# Walk through of a test case.


Given for the following invalid makevalid output, we are going to write a test case for this.

![Issues with makevalid](_images/tc_0.png)

As you can see the makevalid geometry should cover the orange area as well.

For make valid the testcase are kept in the [cases_tests.go](cases_test.go) file.
Go to the end of the makevalidTestCases array, and add a new test case. 

```go
var makevalidTestCases = [...]makevalidCase{
	...
	{ // (4) Circle one
		Description: "circle one",
		MultiPolygon: &geom.MultiPolygon{
			{ // Polygon 
				{ // Ring
					{1286956.1422558832, 6138803.15957211},
					{1286957.5138675969, 6138809.6399925},
					{1286961.0222077654, 6138815.252628375},
					{1286966.228733862, 6138819.3396373615},
					{1286972.5176202222, 6138821.397139203},
					{1286979.1330808033, 6138821.173193399},
					{1286985.2820067848, 6138818.695793352},
					{1286990.1992814348, 6138814.272866236},
					{1286993.3157325392, 6138808.436285537},
					{1286994.2394710402, 6138801.885883152},
					{1286992.8678593265, 6138795.40546864},
					{1286989.3781805448, 6138789.792845784},
					{1286984.1623237533, 6138785.719847463},
					{1286977.864106701, 6138783.662354196},
					{1286971.2486461198, 6138783.872302467},
					{1286965.1183815224, 6138786.349692439},
					{1286960.1824454917, 6138790.7726051165},
					{1286957.084655768, 6138796.623170342},
					{1286956.1422558832, 6138803.15957211},
				},
			},
		},
		ClipBox: geom.NewExtent(
			[2]float64{1286940.46060967, 6138830.2432236},
			[2]float64{1286969.19030943, 6138807.58852643},
		),
		ExpectedMultiPolygon: &geom.MultiPolygon{
			{ // Polygon
				{ // Ring
					{1.2869570796631747e+06, 6.138807588546207e+06},
					{1286969.19030943, 6138807.58852643},
					{1.2869691902517593e+06, 6.138820308571075e+06},
					{1.2869662290238445e+06, 6.138819339761728e+06},
					{1.286966228733862e+06, 6.1388193396373615e+06},
					{1.2869610222077654e+06, 6.138815252628375e+06},
					{1.2869610219360471e+06, 6.138815252311821e+06},
					{1.286957513896204e+06, 6.138809640156404e+06},
				},
			},
		},
	},
}
```

One can run only this test via:

`go test -v -run "TestMakeValid/circle_one"`

This should have the following output which can then be used to diagonise the issue:

```console
=== RUN   TestMakeValid
=== RUN   TestMakeValid/makevalidTestCases_#4_circle_one
2019/02/18 18:15:14 makevalid.go:333: Working on MultiPolygoner: &[[[[1.2869561422558832e+06 6.13880315957211e+06] [1.2869575138675969e+06 6.1388096399925e+06] [1.2869610222077654e+06 6.138815252628375e+06] [1.286966228733862e+06 6.1388193396373615e+06] [1.2869725176202222e+06 6.138821397139203e+06] [1.2869791330808033e+06 6.138821173193399e+06] [1.2869852820067848e+06 6.138818695793352e+06] [1.2869901992814348e+06 6.138814272866236e+06] [1.2869933157325392e+06 6.138808436285537e+06] [1.2869942394710402e+06 6.138801885883152e+06] [1.2869928678593265e+06 6.13879540546864e+06] [1.2869893781805448e+06 6.138789792845784e+06] [1.2869841623237533e+06 6.138785719847463e+06] [1.286977864106701e+06 6.138783662354196e+06] [1.2869712486461198e+06 6.138783872302467e+06] [1.2869651183815224e+06 6.138786349692439e+06] [1.2869601824454917e+06 6.1387907726051165e+06] [1.286957084655768e+06 6.138796623170342e+06]]]]
2019/02/18 18:15:14 makevalid.go:251: *Step  1 : Destructure the geometry into segments w/ the clipbox applied.
2019/02/18 18:15:14 makevalid.go:103: 	hasClipbox: true && !false
2019/02/18 18:15:14 makevalid.go:175: starting seg(   0):LINESTRING (1.2869561422558832e+06 6.13880758852643e+06,1.2869570796631747e+06 6.138807588546207e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(   1):LINESTRING (1.2869570796631747e+06 6.138807588546207e+06,1.286969190251759e+06 6.138807588546206e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(   2):LINESTRING (1.286969190251759e+06 6.138807588546206e+06,1.28696919030943e+06 6.13880758852643e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(   3):LINESTRING (1.286969190251759e+06 6.138807588546206e+06,1.286969190251759e+06 6.138821397104245e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(   4):LINESTRING (1.286969190251759e+06 6.138821397104245e+06,1.2869691902517593e+06 6.138820308571075e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(   5):LINESTRING (1.2869691902517593e+06 6.138820308571075e+06,1.28696919030943e+06 6.13880758852643e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(   6):LINESTRING (1.28696919030943e+06 6.13880758852643e+06,1.28696919030943e+06 6.138821397139203e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(   7):LINESTRING (1.2869561422558832e+06 6.138821397139203e+06,1.286969190251759e+06 6.138821397104245e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(   8):LINESTRING (1.286969190251759e+06 6.138821397104245e+06,1.28696919030943e+06 6.138821397139203e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(   9):LINESTRING (1.2869561422558832e+06 6.13880758852643e+06,1.2869561422558832e+06 6.138821397139203e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(  10):LINESTRING (1.2869570796631747e+06 6.138807588546207e+06,1.2869575138675969e+06 6.1388096399925e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(  11):LINESTRING (1.2869575138675969e+06 6.1388096399925e+06,1.286957513896204e+06 6.138809640156404e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(  12):LINESTRING (1.2869575138675969e+06 6.1388096399925e+06,1.286957513896204e+06 6.138809640156404e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(  13):LINESTRING (1.286957513896204e+06 6.138809640156404e+06,1.2869610219360471e+06 6.138815252311821e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(  14):LINESTRING (1.2869610219360471e+06 6.138815252311821e+06,1.2869610222077654e+06 6.138815252628375e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(  15):LINESTRING (1.2869610219360471e+06 6.138815252311821e+06,1.2869610222077654e+06 6.138815252628375e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(  16):LINESTRING (1.2869610222077654e+06 6.138815252628375e+06,1.286966228733862e+06 6.1388193396373615e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(  17):LINESTRING (1.286966228733862e+06 6.1388193396373615e+06,1.2869662290238445e+06 6.138819339761728e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(  18):LINESTRING (1.286966228733862e+06 6.1388193396373615e+06,1.2869662290238445e+06 6.138819339761728e+06)
2019/02/18 18:15:14 makevalid.go:175: starting seg(  19):LINESTRING (1.2869662290238445e+06 6.138819339761728e+06,1.2869691902517593e+06 6.138820308571075e+06)
2019/02/18 18:15:14 makevalid.go:228: ending seg(   0):LINESTRING (1.2869561422558832e+06 6.13880758852643e+06,1.2869570796631747e+06 6.138807588546207e+06)
2019/02/18 18:15:14 makevalid.go:228: ending seg(   1):LINESTRING (1.2869570796631747e+06 6.138807588546207e+06,1.286969190251759e+06 6.138807588546206e+06)
2019/02/18 18:15:14 makevalid.go:228: ending seg(   2):LINESTRING (1.286969190251759e+06 6.138807588546206e+06,1.28696919030943e+06 6.13880758852643e+06)
2019/02/18 18:15:14 makevalid.go:228: ending seg(   3):LINESTRING (1.286969190251759e+06 6.138807588546206e+06,1.286969190251759e+06 6.138821397104245e+06)
2019/02/18 18:15:14 makevalid.go:228: ending seg(   4):LINESTRING (1.286969190251759e+06 6.138821397104245e+06,1.2869691902517593e+06 6.138820308571075e+06)
2019/02/18 18:15:14 makevalid.go:228: ending seg(   5):LINESTRING (1.2869691902517593e+06 6.138820308571075e+06,1.28696919030943e+06 6.13880758852643e+06)
2019/02/18 18:15:14 makevalid.go:228: ending seg(   6):LINESTRING (1.28696919030943e+06 6.13880758852643e+06,1.28696919030943e+06 6.138821397139203e+06)
2019/02/18 18:15:14 makevalid.go:228: ending seg(   7):LINESTRING (1.2869561422558832e+06 6.138821397139203e+06,1.286969190251759e+06 6.138821397104245e+06)
2019/02/18 18:15:14 makevalid.go:228: ending seg(   8):LINESTRING (1.286969190251759e+06 6.138821397104245e+06,1.28696919030943e+06 6.138821397139203e+06)
2019/02/18 18:15:14 makevalid.go:228: ending seg(   9):LINESTRING (1.2869561422558832e+06 6.13880758852643e+06,1.2869561422558832e+06 6.138821397139203e+06)
2019/02/18 18:15:14 makevalid.go:228: ending seg(  10):LINESTRING (1.2869570796631747e+06 6.138807588546207e+06,1.2869575138675969e+06 6.1388096399925e+06)
2019/02/18 18:15:14 makevalid.go:228: ending seg(  11):LINESTRING (1.2869575138675969e+06 6.1388096399925e+06,1.286957513896204e+06 6.138809640156404e+06)
2019/02/18 18:15:14 makevalid.go:228: ending seg(  12):LINESTRING (1.286957513896204e+06 6.138809640156404e+06,1.2869610219360471e+06 6.138815252311821e+06)
2019/02/18 18:15:14 makevalid.go:228: ending seg(  13):LINESTRING (1.2869610219360471e+06 6.138815252311821e+06,1.2869610222077654e+06 6.138815252628375e+06)
2019/02/18 18:15:14 makevalid.go:228: ending seg(  14):LINESTRING (1.2869610222077654e+06 6.138815252628375e+06,1.286966228733862e+06 6.1388193396373615e+06)
2019/02/18 18:15:14 makevalid.go:228: ending seg(  15):LINESTRING (1.286966228733862e+06 6.1388193396373615e+06,1.2869662290238445e+06 6.138819339761728e+06)
2019/02/18 18:15:14 makevalid.go:228: ending seg(  16):LINESTRING (1.2869662290238445e+06 6.138819339761728e+06,1.2869691902517593e+06 6.138820308571075e+06)
2019/02/18 18:15:14 makevalid.go:269: Step   2 : Convert segments to linestrings to use in triangleuation.
2019/02/18 18:15:14 makevalid.go:280: triangulating segs(17)
2019/02/18 18:15:14 makevalid.go:282: seg(   0):LINESTRING (1.2869561422558832e+06 6.13880758852643e+06,1.2869570796631747e+06 6.138807588546207e+06)
2019/02/18 18:15:14 makevalid.go:282: seg(   1):LINESTRING (1.2869570796631747e+06 6.138807588546207e+06,1.286969190251759e+06 6.138807588546206e+06)
2019/02/18 18:15:14 makevalid.go:282: seg(   2):LINESTRING (1.286969190251759e+06 6.138807588546206e+06,1.28696919030943e+06 6.13880758852643e+06)
2019/02/18 18:15:14 makevalid.go:282: seg(   3):LINESTRING (1.286969190251759e+06 6.138807588546206e+06,1.286969190251759e+06 6.138821397104245e+06)
2019/02/18 18:15:14 makevalid.go:282: seg(   4):LINESTRING (1.286969190251759e+06 6.138821397104245e+06,1.2869691902517593e+06 6.138820308571075e+06)
2019/02/18 18:15:14 makevalid.go:282: seg(   5):LINESTRING (1.2869691902517593e+06 6.138820308571075e+06,1.28696919030943e+06 6.13880758852643e+06)
2019/02/18 18:15:14 makevalid.go:282: seg(   6):LINESTRING (1.28696919030943e+06 6.13880758852643e+06,1.28696919030943e+06 6.138821397139203e+06)
2019/02/18 18:15:14 makevalid.go:282: seg(   7):LINESTRING (1.2869561422558832e+06 6.138821397139203e+06,1.286969190251759e+06 6.138821397104245e+06)
2019/02/18 18:15:14 makevalid.go:282: seg(   8):LINESTRING (1.286969190251759e+06 6.138821397104245e+06,1.28696919030943e+06 6.138821397139203e+06)
2019/02/18 18:15:14 makevalid.go:282: seg(   9):LINESTRING (1.2869561422558832e+06 6.13880758852643e+06,1.2869561422558832e+06 6.138821397139203e+06)
2019/02/18 18:15:14 makevalid.go:282: seg(  10):LINESTRING (1.2869570796631747e+06 6.138807588546207e+06,1.2869575138675969e+06 6.1388096399925e+06)
2019/02/18 18:15:14 makevalid.go:282: seg(  11):LINESTRING (1.2869575138675969e+06 6.1388096399925e+06,1.286957513896204e+06 6.138809640156404e+06)
2019/02/18 18:15:14 makevalid.go:282: seg(  12):LINESTRING (1.286957513896204e+06 6.138809640156404e+06,1.2869610219360471e+06 6.138815252311821e+06)
2019/02/18 18:15:14 makevalid.go:282: seg(  13):LINESTRING (1.2869610219360471e+06 6.138815252311821e+06,1.2869610222077654e+06 6.138815252628375e+06)
2019/02/18 18:15:14 makevalid.go:282: seg(  14):LINESTRING (1.2869610222077654e+06 6.138815252628375e+06,1.286966228733862e+06 6.1388193396373615e+06)
2019/02/18 18:15:14 makevalid.go:282: seg(  15):LINESTRING (1.286966228733862e+06 6.1388193396373615e+06,1.2869662290238445e+06 6.138819339761728e+06)
2019/02/18 18:15:14 makevalid.go:282: seg(  16):LINESTRING (1.2869662290238445e+06 6.138819339761728e+06,1.2869691902517593e+06 6.138820308571075e+06)
2019/02/18 18:15:14 triangulate.go:16: Step   3 : generate triangles
triangulations of segs(17) took 630.05µs
2019/02/18 18:15:14 triangulate.go:31: Step   4 : label triangles and discard outside triangles
2019/02/18 18:15:14 makevalid.go:287: triangulations of segs(17) took 842.621µs
Got 11 trinangles
2019/02/18 18:15:14 makevalid.go:295:    0: POLYGON ((1.2869561422558832e+06 6.138821397139203e+06,1.2869610222077654e+06 6.138815252628375e+06,1.286966228733862e+06 6.1388193396373615e+06,1.2869561422558832e+06 6.138821397139203e+06))
2019/02/18 18:15:14 makevalid.go:295:    1: POLYGON ((1.2869570796631747e+06 6.138807588546207e+06,1.28696919030943e+06 6.13880758852643e+06,1.286969190251759e+06 6.138807588546206e+06,1.2869570796631747e+06 6.138807588546207e+06))
2019/02/18 18:15:14 makevalid.go:295:    2: POLYGON ((1.2869570796631747e+06 6.138807588546207e+06,1.286969190251759e+06 6.138807588546206e+06,1.2869575138675969e+06 6.1388096399925e+06,1.2869570796631747e+06 6.138807588546207e+06))
2019/02/18 18:15:14 makevalid.go:295:    3: POLYGON ((1.2869561422558832e+06 6.13880758852643e+06,1.2869575138675969e+06 6.1388096399925e+06,1.286957513896204e+06 6.138809640156404e+06,1.2869561422558832e+06 6.13880758852643e+06))
2019/02/18 18:15:14 makevalid.go:295:    4: POLYGON ((1.286957513896204e+06 6.138809640156404e+06,1.2869575138675969e+06 6.1388096399925e+06,1.286969190251759e+06 6.138807588546206e+06,1.286957513896204e+06 6.138809640156404e+06))
2019/02/18 18:15:14 makevalid.go:295:    5: POLYGON ((1.286957513896204e+06 6.138809640156404e+06,1.286969190251759e+06 6.138807588546206e+06,1.2869610219360471e+06 6.138815252311821e+06,1.286957513896204e+06 6.138809640156404e+06))
2019/02/18 18:15:14 makevalid.go:295:    6: POLYGON ((1.2869610219360471e+06 6.138815252311821e+06,1.286969190251759e+06 6.138807588546206e+06,1.2869610222077654e+06 6.138815252628375e+06,1.2869610219360471e+06 6.138815252311821e+06))
2019/02/18 18:15:14 makevalid.go:295:    7: POLYGON ((1.2869610222077654e+06 6.138815252628375e+06,1.286969190251759e+06 6.138807588546206e+06,1.286966228733862e+06 6.1388193396373615e+06,1.2869610222077654e+06 6.138815252628375e+06))
2019/02/18 18:15:14 makevalid.go:295:    8: POLYGON ((1.286966228733862e+06 6.1388193396373615e+06,1.286969190251759e+06 6.138807588546206e+06,1.2869662290238445e+06 6.138819339761728e+06,1.286966228733862e+06 6.1388193396373615e+06))
2019/02/18 18:15:14 makevalid.go:295:    9: POLYGON ((1.2869691902517593e+06 6.138820308571075e+06,1.2869662290238445e+06 6.138819339761728e+06,1.286969190251759e+06 6.138807588546206e+06,1.2869691902517593e+06 6.138820308571075e+06))
2019/02/18 18:15:14 makevalid.go:295:   10: POLYGON ((1.2869691902517593e+06 6.138820308571075e+06,1.286969190251759e+06 6.138807588546206e+06,1.28696919030943e+06 6.13880758852643e+06,1.2869691902517593e+06 6.138820308571075e+06))
2019/02/18 18:15:14 makevalid.go:297: Step   5 : generate multipolygon from triangles
2019/02/18 18:15:14 makevalid.go:341: Returning on MultiPolygon: *geom.MultiPolygon
--- FAIL: TestMakeValid (0.00s)
    --- FAIL: TestMakeValid/makevalidTestCases_#4_circle_one (0.00s)
        makevalid_test.go:69: mulitpolygon, expected &[[[[1.2869570796631747e+06 6.138807588546207e+06] [1.28696919030943e+06 6.13880758852643e+06] [1.2869691902517593e+06 6.138820308571075e+06] [1.2869662290238445e+06 6.138819339761728e+06] [1.286966228733862e+06 6.1388193396373615e+06] [1.2869610222077654e+06 6.138815252628375e+06] [1.2869610219360471e+06 6.138815252311821e+06] [1.286957513896204e+06 6.138809640156404e+06]]]] got &[[[[1.2869561422558832e+06 6.13880758852643e+06] [1.2869575138675969e+06 6.1388096399925e+06] [1.2869570796631747e+06 6.138807588546207e+06] [1.28696919030943e+06 6.13880758852643e+06] [1.2869691902517593e+06 6.138820308571075e+06] [1.2869662290238445e+06 6.138819339761728e+06] [1.286966228733862e+06 6.1388193396373615e+06] [1.2869561422558832e+06 6.138821397139203e+06] [1.2869610222077654e+06 6.138815252628375e+06] [1.2869610219360471e+06 6.138815252311821e+06] [1.286957513896204e+06 6.138809640156404e+06]]]]
        makevalid_test.go:71: Got:
            MULTIPOLYGON (((1.2869561422558832e+06 6.13880758852643e+06,1.2869575138675969e+06 6.1388096399925e+06,1.2869570796631747e+06 6.138807588546207e+06,1.28696919030943e+06 6.13880758852643e+06,1.2869691902517593e+06 6.138820308571075e+06,1.2869662290238445e+06 6.138819339761728e+06,1.286966228733862e+06 6.1388193396373615e+06,1.2869561422558832e+06 6.138821397139203e+06,1.2869610222077654e+06 6.138815252628375e+06,1.2869610219360471e+06 6.138815252311821e+06,1.286957513896204e+06 6.138809640156404e+06,1.2869561422558832e+06 6.13880758852643e+06)))
            Expected:
            MULTIPOLYGON (((1.2869570796631747e+06 6.138807588546207e+06,1.28696919030943e+06 6.13880758852643e+06,1.2869691902517593e+06 6.138820308571075e+06,1.2869662290238445e+06 6.138819339761728e+06,1.286966228733862e+06 6.1388193396373615e+06,1.2869610222077654e+06 6.138815252628375e+06,1.2869610219360471e+06 6.138815252311821e+06,1.286957513896204e+06 6.138809640156404e+06,1.2869570796631747e+06 6.138807588546207e+06)))
            ClipBox:
            POLYGON ((1.28694046060967e+06 6.13880758852643e+06,1.28696919030943e+06 6.13880758852643e+06,1.28696919030943e+06 6.1388302432236e+06,1.28694046060967e+06 6.1388302432236e+06,1.28694046060967e+06 6.13880758852643e+06))
            Original Geometry:
            MULTIPOLYGON (((1.2869561422558832e+06 6.13880315957211e+06,1.2869575138675969e+06 6.1388096399925e+06,1.2869610222077654e+06 6.138815252628375e+06,1.286966228733862e+06 6.1388193396373615e+06,1.2869725176202222e+06 6.138821397139203e+06,1.2869791330808033e+06 6.138821173193399e+06,1.2869852820067848e+06 6.138818695793352e+06,1.2869901992814348e+06 6.138814272866236e+06,1.2869933157325392e+06 6.138808436285537e+06,1.2869942394710402e+06 6.138801885883152e+06,1.2869928678593265e+06 6.13879540546864e+06,1.2869893781805448e+06 6.138789792845784e+06,1.2869841623237533e+06 6.138785719847463e+06,1.286977864106701e+06 6.138783662354196e+06,1.2869712486461198e+06 6.138783872302467e+06,1.2869651183815224e+06 6.138786349692439e+06,1.2869601824454917e+06 6.1387907726051165e+06,1.286957084655768e+06 6.138796623170342e+06,1.2869561422558832e+06 6.13880315957211e+06)))
FAIL
exit status 1
FAIL	github.com/go-spatial/geom/planar/makevalid	0.007s
```

If the output from tests are not the same, make sure you have debug set to true.

This output should help identifiy where the code is failing. In our example above there are two possible places, the Destructuring of the lines, or the trinagles that are produced from 
the destructured line segments or the labeling. Taking the WKT output and putting it into qGIS can help visualize where the issues may lie.


## running tests in docker-environment

In case you don't have go installed, you can also run these tests inside a docker-container.

grab some go-image: `docker pull golang:1.11.0-alpine3.8`
cd into your `geom` folder and startup a container: `docker run --rm -ti -v $(pwd):/go/src/geom/vendor/github.com/go-spatial/geom golang:1.11.0-alpine3.8`

```
apk add git build-base
apk add --repository http://dl-cdn.alpinelinux.org/alpine/edge/testing libspatialite-dev
cd /go/src/geom/vendor/github.com
git clone --branch master --depth 1 https://github.com/mattn/go-sqlite3.git mattn/go-sqlite3
git clone --branch master --depth 1 https://github.com/pborman/uuid.git pborman/uuid
git clone --branch master --depth 1 https://github.com/google/uuid.git google/uuid
cd /go/src/geom/vendor/github.com/go-spatial/geom/planar/makevalid
go test -v -run "TestMakeValid"
```
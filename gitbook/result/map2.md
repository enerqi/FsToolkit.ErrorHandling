## Result.map2

Namespace: `FsToolkit.ErrorHandling`

Function Signature:

```
('a -> 'b -> 'c) -> Result<'a, 'd> -> Result<'b, 'd> 
  -> Result<'c, 'd>
```

## Examples:

### Example 1

Let's assume that we have a `add` function which adds two numbers.

```fsharp
// int -> int -> int
let add a b = a + b
```

And an another function that converts string to an integer

```fsharp
open System

// string -> Result<int, string>
let tryParseInt str =
  match Int32.TryParse str with
  | true, x -> Ok x
  | false, _ -> 
    Error (sprintf "unable to parse '%s' to integer" str)
```

With the help of `Result.map2` function, we can now do the following

```fsharp
open FsToolkit.ErrorHandling

let result =
  Result.map2 add (tryParseInt "40") (tryParseInt "2")
  // returns - Ok 42
```

```fsharp
open FsToolkit.ErrorHandling
let result =
  Result.map2 add (tryParseInt "40") (tryParseInt "foobar")
  // returns - Error "unable to parse 'foobar' to integer"
```

### Example 2

Let's assume that we have the following types

#### Latitude

```fsharp
type Latitude = private Latitude of double with
  // double
  member this.Value =
    let (Latitude lat) = this
    lat
  // double -> Result<Latitude, string>
  static member TryCreate (lat : double) =
    if lat > -180. && lat < 180. then
      Ok (Latitude lat)
    else
      sprintf "%A is a invalid latitude value" lat |> Error 
```

#### Longitude

```fsharp
type Longitude = private Longitude of double with
  // double
  member this.Value =
    let (Longitude lng) = this
    lng
  // double -> Result<Longitude, string>
  static member TryCreate (lng : double) =
    if lng > -90. && lng < 90. then
      Ok (Longitude lng)
    else
      sprintf "%A is a invalid longitude value" lng |> Error 
```

#### Location

```fsharp
type Location = {
  Latitude : Latitude
  Longitude : Longitude
}
```

And also a function to create `Location`

```fsharp
let location lat lng =
  {Latitude = lat; Longitude = lng}
```

Then, we can use the `Result.map2` function as below to create the location with validation

```fsharp
let validLatR = Latitude.TryCreate 13.067439
let validLngR = Longitude.TryCreate 80.237617 

open FsToolkit.ErrorHandling

let result =
  Result.map2 location validLatR validLngR
(* returns - Ok {Latitude = Latitude {Value = 13.067439;};
                 Longitude = Longitude {Value = 80.237617;};} *)
```

When we try with an invalid latitude value, we'll get the following result

```fsharp
let invalidLatR = Latitude.TryCreate 200.
let result =
  Result.map2 location invalidLatR validLngR
  // returns - Error "200.0 is a invalid latitude value"
```
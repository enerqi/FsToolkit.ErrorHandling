## Result Computation Expression

Namespace: `FsToolkit.ErrorHandling.CE.ResultOption`

## Examples:

### Example 1

```fsharp
open FsToolkit.ErrorHandling.CE.ResultOption

let add x y z = x + y + z

let addResult : Result<int option, string> = resultOption {
  let! x = Ok (Some 30)
  let! y = Ok (Some 10)
  let! z = Ok (Some 2)
  return add3 x y z
}
```

### Example 2

The [ResultOption.map2 example](../resultOption/map2.md#example-2) can be written using `resultOption` computation expression as below

```fsharp
// CreatePostRequestDto -> Result<CreatePostRequest, string>
let toCreatePostRequest (dto :CreatePostRequestDto) = 

  // Result<Location option, string>
  let locationR = resultOption {
    let! lat = 
      dto.Latitude
      |> Option.traverseResult Latitude.TryCreate 
    let! lng = 
      dto.Longitude
      |> Option.traverseResult Longitude.TryCreate
    return location lat lng
  }
  
  // Result<Tweet, string>
  let tweetR = Tweet.TryCreate dto.Tweet

  // Result<CreatePostRequest, string>
  Result.map2 createPostRequest2 tweetR locationR
```
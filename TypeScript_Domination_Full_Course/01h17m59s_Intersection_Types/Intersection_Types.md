intersection types


what is union in ts
let a:string| null

..but inttersection is like 
type User={
    name:string,
    email:string,
}

type Admin=User&{
    getDetails(user:string):void
}

method ...tats the main use if we extends it

what it is basically..the shape of object


difference is types job is to define type or merger or union or single type define..interface comes to action to make the shape of object
Interfaces and Type Aliases
- defining interfaces
- using interfaces to define object shapes
- extending interfaces
- type aliases
- intersection types

types..
use case...
we have a function with typed parimiter fof a funciton.
letssay we have a object as parameter but the interface says what will be the thngs in the object
so we use interface User{
    name:string.
    email:string
}

function abcd(obj:User){
    ...
}

now if we use this funciton..w need to send object which has exactly same thing as interface no matter what

give proper example

now even ifwe use anyobject in paremeter it must be same as that interface for that funtion since declaring the function we tld that all upcoming object in this parameter will follow the interface...no more, no less, no missing... exact thing.. how to handel things ..all
 
 tips and tricks  and use of ? to make optional parameter in interface....handle it ..

 meaning if the sending obj in parameter follow the interface and the interface have a element that has an optional field the obj can skip it

 allunder thehood
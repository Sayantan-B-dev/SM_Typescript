what are enums...how to use it..syntax..

enum UserRoles{
    ADMIN="admin",
    Guest="guest",
    SUPER_ADMIN="super_admin"
}

enum StatusCode{
    ...
    ...
}

how to acces these
/...how does it look in js after comiling..like 
(function (UserRoles){
    UserRoles["ADMIN"]="admin"
    ...
})(UserRoles||(UserRoles={}))

whats its doing behind the hood
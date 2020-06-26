This is a write up for the tux-fanpage challenge for the 2020 redpwnCTF competition. This challenge is one that I found very interesting as I love challenges that use URL parameter tampering. And honestly, I spent way too much time on it before figuring it out LOL.

# Challenge intro

```
My friend made a fanpage for Tux; can you steal the source code for me?

Site: tux-fanpage.2020.redpwnc.tf
```

The challenge also provides the source file for index.js.


After downloading the code and reviewing, one of the first things I noticed in it was the path traversal prevention. The code finds any `../` strings in the path string and replaces them with an empty string, effectively removing direct path traversal attempts (even with URL encoding applied).

```
//Prevent directory traversal attack
function preventTraversal(dir){
    if(dir.includes('../')){
        let res = dir.replace('../', '')
        return preventTraversal(res)
    }

    //In case people want to test locally on windows
    if(dir.includes('..\\')){
        let res = dir.replace('..\\', '')
        return preventTraversal(res)
    }
    return dir
}

//Get absolute path from relative path
function prepare(dir){
    return path.resolve('./public/' + dir)
}

//Strip leading characters
function strip(dir){
    const regex = /^[a-z0-9]$/im

    //Remove first character if not alphanumeric
    if(!regex.test(dir[0])){
        if(dir.length > 0){
            return strip(dir.slice(1))
        }
        return ''
    }

    return dir
}
```

After exhausting all possible encoding inputs for path traversal, I remembered that I didn't only have to submit the path parameter as a string. If the application was expecting a string but did not do any type validation, it would be possible to change the logic of the code by passing it a type that it wasn't designed to work with such as an object or an array!

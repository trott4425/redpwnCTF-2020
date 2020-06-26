This is a write up for the tux-fanpage challenge for the 2020 redpwnCTF competition. This challenge is one that I found interesting and honestly spent way too much time on.

# Challenge intro

```
My friend made a fanpage for Tux; can you steal the source code for me?

Site: tux-fanpage.2020.redpwnc.tf
```

The challenge also provides the source file for index.js.


One of the first things I noticed in the code was the path traversal prevention. The code finds any `../` strings in the path string and replaces them with an empty string, effectively removing direct path traversal attempts (even with URL encoding applied).

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

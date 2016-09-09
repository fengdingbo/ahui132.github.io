# bind params
http://stackoverflow.com/questions/256754/how-to-pass-arguments-to-addeventlistener-listener-function/36786630#36786630

## bind params via bind
bind params by value(not by Reference)

    var a = 'before'
    function echo(a, b, c){
        console.log(a,b,c);
    }
    function caller(callback){
        callback(2,3);
    }
    caller(echo.bind(null,1));//bind
    a = 'after'

## bind params via anonymous func's closure
You could pass somevar by value(not by reference) via a javascript feature known as [closure][1]:

    var someVar=1;
    func = function(v){
        console.log(v);
    }
    document.addEventListener('click',function(someVar){
        return function(){func(someVar)}
    }(someVar));
    someVar=2

In generic, you could write a common wrap function such as wrapEventCallback

    function wrapEventCallback(callback){
        var args = Array.prototype.slice.call(arguments, 1);
        return function(e){
            callback.apply(this, args)
        }
    }
    var someVar=1;
    func = function(v){
        console.log(v);
    }
    document.addEventListener('click',wrapEventCallback(func,someVar))
    someVar=2

## bind via event.target

    var someInput = document.querySelector('input');
    someInput.addEventListener('click', myFunc, false);
    someInput.myParam = 'This is my parameter';
    function myFunc(evt) {
      window.alert( evt.target.myParam );
    }

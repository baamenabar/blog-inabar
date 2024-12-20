---
title: LocalStorage through Store.sync() on Sencha Touch 2
classes: language-javascript
date: 2012-10-02 17:13:46
tags: ["javascript", "sencha", "touch", "Cordova", "Phonegap", "localstorage"]
---

# LocalStorage through Store.sync() on Sencha Touch 2

Storing persistent information in a sencha app. Cookies aren't available for all devices, while localstorege has wonderful support with Phonegap (Cordova) that converts it in a very stable storing system.

```js
Ext.define('MyApp.store.LocalStuff',{
extend: 'Ext.data.Store',
xtype:'localstuff',
config: {
fields:['stuffId','stuffName','stuffArray'],
storeId:'LocalStuffId',
proxy:{
type:'localstorage',
id:'idForLocalStorage',
reader: {
type: 'json',
rootProperty: 'stuffArray'
}
}
}
});
```

Assuming you've populated the store with something like:

```js
{'stuffArray':[
{'stuffId':'130', 'stuffName':'floob', 'stuffArray':[
'first',
'second',
'tird' //here be a typo (on purpose)
]
},
{'stuffId':'131', 'stuffName':'sateo', 'stuffArray':[
'primero',
'segundo',
'tercero'
]
},
{'stuffId':'133', 'stuffName':'tolus', 'stuffArray':[
'prima',
'secunda',
'tertia'
]
}
]
}
// to fix the first's record misspelling you might be tempted to do the following

{//in a given controller or view\
someEventHandler:function(){\
var stor = Ext.getStore('LocalStuffId');\
var firstRecord = stor.getAt(0);//which will get you a record in the form of a Model\
firstRecord.get('stuffArray')[2]='third';//that will fix it\
//now we just sync() to make the change persist when we close the app\
stor.sync();\
}\
}
```

If you tried this there will be no errors on the console, so, all is ok. Right?  
wrong! The changes won't persist on restart. Why?  
Because the records are “clean”.  
How can we “dirty” them up?  
The only way I found (I'm sure there are plenty more) is by using the method **.set()** from the Model to update the record, that'll tell the store it's dirty  
That sounds odd to my untrained brain on MVC, better fixing the above example.

```js
{//in a given controller or view\
someEventHandler:function(){\
var stor = Ext.getStore('LocalStuffId');\
var firstRecord = stor.getAt(0);//which will get you a record in the form of a Model\
//you might go around the following 2 lines in many ways, the important part is .set() after that\
var theArrayToFix = firstRecord.get('stuffArray');\
theArrayToFix[2]='third';

firstRecord.set('stuffArray',theArrayToFix);//set() dirties the record\
//NOW we sync() to make the change persist when we close the app\
stor.sync();\
}\
}
```

These are just snippets, I'll be documenting full examples soon.

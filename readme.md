
# 4.0
## import 
```javascript 
import { O_json_db} from "http://deno.land/x/o_json_db/O_json_db.module.js"
```
## instance 
```javascript
// instance
var o_json_db = new O_json_db();
```
## class is required
a javascript class is required
```javascript
class O_animal{
    constructor(s_name){
        this.n_id = 0; 
        this.s_name = s_name
    }
}
```
## full example, import, instanciate, create, read, update, delete
```javascript

import { O_json_db} from "./O_json_db.module.js"
// import { O_json_db} from "https://deno.land/x/o_json_db@4.0/O_json_db.module.js"

class O_animal{
    constructor(
        n_id = null,
        s_name){
        this.n_id = n_id; 
        this.s_name = s_name
    }
}
// instance
var o_json_db = new O_json_db();

//create
var a_o = await o_json_db.f_a_o_create(
    O_animal,
    new O_animal(null,'leguan')
);
console.log(a_o)

var a_o = await o_json_db.f_a_o_create(
    O_animal,
    new O_animal(18,'barracuda') //manual n_id
);
console.log(a_o)

var a_o = await o_json_db.f_a_o_create(
    O_animal,
    new O_animal(null,'wale')
);
console.log(a_o)

var a_o = await o_json_db.f_a_o_create(
    O_animal,
    new O_animal(undefined,'kangoroo')
);
console.log(a_o)

var a_o = await o_json_db.f_a_o_create(
    O_animal,
    new O_animal(0,'anaconda') // n_id 0 counts as null or undefined
);
console.log(a_o)

// read
var a_o = (await o_json_db.f_a_o_read(
    O_animal,
    function(o){
        return o.s_name.includes("le")
    }
));
console.log(a_o)

//update
var a_o = (await o_json_db.f_a_o_update(
    O_animal,
    function(o){
        return o.s_name.includes("le")
    },
    function(o){
        o.s_name = `${o.s_name}: after update ${o.s_name.toUpperCase()}`
    }, 
));
console.log(a_o)
```

# <=3.1
## you need a class/model 
in order to store a set of data you need a model
```javascript
class O_test{
    constructor(s_name){
        this.n_id = 0; 
        this.s_name = s_name
    }
}
```
### imports
```javascript
// import the model 
import { O_test } from "...O_test.module.js"
// import the datamanger
import { O_json_db} from "http://deno.land/x/o_json_db/O_json_db.module.js"
```
## create instance
```javascript
const o_json_db = new O_json_db();
```
## create/insert a new entry/object
```javascript
//create some objects
var o = await o_json_db.f_o_create(new O_test('leguan')); 
console.log(o)
var o = await o_json_db.f_o_create(new O_test('cat')); 
console.log(o)
var o = await o_json_db.f_o_create(new O_test('gorilla')); 
console.log(o)
```

## select * / get all 
```javascript
var a_o = await o_json_db.f_a_o_read(
    O_test,
    {}
);
console.log(a_o)
```
## select/read/find statement
in this example find where n_id is 5
```javascript
console.log("read with criterium (n_id : 5)")
var a_o = await o_json_db.f_a_o_read(
    O_test,
    {n_id: 5}
);
console.log(a_o)
```
where s_name == "hello"
```javascript
console.log("read with criterium (s_name: 'hello')")
var a_o = await o_json_db.f_a_o_read(
    O_test,
    {s_name: "hello"}
);
console.log(a_o)
```

## delete/remove/destroy/unset where 
```javascript
console.log("delete with criterium {s_name: 'hello'}")
var a_o = await o_json_db.f_a_o_delete(
    O_test, 
    {
        s_name: "hello"
    }
)
```
## update/modify entries 
```javascript
console.log("update with criterium {s_name: 'leguan'}, updated {s_name: 'iguana'}") 
var a_o = await o_json_db.f_a_o_update(
    O_test,
    {s_name:"leguan"},
    {s_name:"iguana"}
);
console.log(a_o)
```
# o_json_db_config.module.js_config.json
example of the config 
every model will get the timestamps
```json
{
    "a_s_class_name_default_timestamp_disabled": [], 
    "a_s_class_name_default_timestamp_enabled": ["*"]
}
```
only the model with the name 'O_test' will get the timestamps
```json
{
    "a_s_class_name_default_timestamp_disabled": [], 
    "a_s_class_name_default_timestamp_enabled": ["O_test"]
}
```
only the model with the name 'O_test' will NOT get the timestamps
```json
{
    "a_s_class_name_default_timestamp_disabled": ["O_test"], 
    "a_s_class_name_default_timestamp_enabled": ["*"]
}
```
# custom timestamps
timestamps are added/updated via simple callback functions
which are runned before the data is stored in a file 

do the following to add custom timestamps
## disable default timestamps
```javascript 
{
    // ...
    "a_s_class_name_default_timestamp_disabled": ["*"], 
    "a_s_class_name_default_timestamp_enabled": [""]
    // ...
}
```
## add custom callback functions
```javascript
o_json_db.a_o_callback.push(
    new O_json_db_callback(
        function(o_class, o_instance){

            if(
                self.o_config.o.a_s_class_name_default_timestamp_enabled.includes('*')
                ||
                self.o_config.o.a_s_class_name_default_timestamp_enabled.includes(o_class.name)
                ){
                    if(
                        self.o_config.o.a_s_class_name_default_timestamp_disabled.includes(o_class.name) == false
                    ){
                        var o_date = new Date()
                        o_instance.n_ts_ut_ms_created = o_date.getTime();
                        o_instance.s_ts_ut_created =  o_date.toString();
                    }
            }
        }, 
        "create", // 
    )
)
```

# The Testing World-API-testing-with-Postman

This project demonstrates API testing using Postman, providing a collection of tests to validate various endpoints of the API.


##  Features
- Tests for GET, POST, PUT, DELETE requests
- Collection of tests covering different API endpoints
- Environment setup for easy switching between environments
- Pre-request scripts for data setup
- Test scripts for assertions and validations
## API Documentation
https://documenter.getpostman.com/view/34618466/2sAYkKGx4r
## Technology used:
- Postman
- Newman
## Prerequisite:
- Node Js
- Newman
- Newman Html Report Library
## Installation
1. Postman: If you haven't already, [download and install Postman.](https://www.postman.com/downloads/)
2. Clone the repository:
```
https://github.com/shobuj-das/Testing-World-API-testing-with-Postman.git
```

3. Import the Postman collection:
   - Open Postman.
   - Click on the Import button.
   - Select the file from the repository.

4. Import the Postman environment:
   - In Postman, click on the gear icon in the top right corner.
   - Select Import and choose the file.

5. Newman and Report Installation Process:
   - Newman Install Command:
   ```
    npm install -g newman
    ```
   - Newman Html Report Install Command:
   ```
    npm install -g newman-reporter-htmlextra
   ```

   
## Uses

1. Select Environment:
   - In Postman, select the appropriate environment (e.g., Development, Production) from the top-right dropdown.
2. Run Collection:
   - Select the imported collection from the Collections sidebar.
   - Click on the Runner button to open the collection runner.
   - Select the desired environment.
   - Click Start Test to run the collection.
3. View Results:
   - Once the tests are complete, view the results in the Runner tab.
   - Detailed test results can be viewed for each request.
## Test Case Scenarios
### 1. Get Students

#### Request URL: https://thetestingworldapi.com/api/studentsDetails
#### Request Method: GET

#### Response body:
```
[
    {
        "id": 10523513,
        "first_name": "Bessie",
        "middle_name": "O'Hara",
        "last_name": "Bruen",
        "date_of_birth": "1977-6-18"
    },
    {
        "id": 10523505,
        "first_name": "vicky",
        "middle_name": "balaji",
        "last_name": "Deva",
        "date_of_birth": "08-03-2025"
    }
]    
```
#### Post-response scripts
```

var jsonData = pm.response.json();
var status_code = pm.response.code;
console.log(status_code)

switch(status_code){
    case 200:
        pm.test("Status Code 200");

        pm.test("Verify Status Message",function(){
            pm.expect(pm.response.status).to.eql("OK");
        });

        pm.test("Verify Response length", function(){
        pm.expect(jsonData.length).to.equal(100)
        });

        
        console.log(pm.request.url.toString());

        break;
    
    case 404:
        pm.test("Verify Error code: 404")
        pm.test("Verify error message", function(){
            pm.expect(pm.response.status).to.eql("Not Found");
        });
        break;

    case 500:
        pm.test("Verify Error code: 500");
        pm.test("Verify error message",function(){
            pm.expect(pm.response).to.eql("Internal Server Error");
        })
        break;

    default:
        break;
}
```

### 2. Create Student
#### Request URL: https://thetestingworldapi.com/api/studentsDetails
#### Request Method: POST
#### Pre-request script
```
var first_name = pm.variables.replaceIn("{{$randomFirstName}}");
pm.environment.set("first_name", first_name);

var middle_name = pm.variables.replaceIn("{{$randomLastName}}");
pm.environment.set("middle_name", middle_name);

var last_name = pm.variables.replaceIn("{{$randomLastName}}");
pm.environment.set("last_name", last_name);

//------------------------------------------------------------------
//-----  generate a random year between 1950-1999 
var random_int = Math.floor(Math.random() * 50) + 50;
var year = "19" + random_int.toString();
// ------ Generate a random day between 1 to 30
var random_int = Math.floor(Math.random() * 30 ) + 1;
var day = random_int.toString();
// --- Generate a rondom month between 1 to 12
var random_int = Math.floor(Math.random() * 12) + 1;
var month = random_int.toString();
// add in a sigle string
var data_of_birth = year + "-" + month + "-" + day;

pm.environment.set("date_of_birth", data_of_birth);
console.log(data_of_birth);
```
#### Request body:
```
{ 
"first_name": "{{first_name}}", 
"middle_name": "{{middle_name}}", 
"last_name": "{{last_name}}", 
"date_of_birth": "{{date_of_birth}}"
}
```
#### Response Body:
```
{
    "id": 10523513,
    "first_name": "Bessie",
    "middle_name": "O'Hara",
    "last_name": "Bruen",
    "date_of_birth": "1977-6-18"
}
```
#### Post-response scripts:
```

var status_code = pm.response.code;

switch(status_code){
    case 201:
        pm.test("Verify Status code 201");
        pm.test("Verify Status Message", function(){
            pm.expect(pm.response.status).to.eql("Created");
        })
        var jsonData = pm.response.json();
        pm.environment.set("id", jsonData.id);

        const expSchema = 
        {
        "type": "object",
        "properties": {
            "id": {
            "type": "integer"
            },
            "first_name": {
            "type": "string"
            },
            "middle_name": {
            "type": "string"
            },
            "last_name": {
            "type": "string"
            },
            "date_of_birth": {
            "type": "string"
            }
        },
        "required": [
            "id",
            "first_name",
            "middle_name",
            "last_name",
            "date_of_birth"
        ]
        };
        pm.test("Verify Response Json Schema", function(){
            pm.response.to.have.jsonSchema(expSchema);
        })
        

        break;


    case 500:
        pm.test("Verify Internal server error status code: 500");
        pm.test("Verify Error message is correct", function(){
            pm.expect(pm.response.status).to.eql("Internal Server Error");
        });

        const exp_schema_500 = {
            "type": "object",
            "properties": {
                "Message": {
                "type": "string"
                }
            },
            "required": [
                "Message"
            ]
            }
        pm.test("Error Message schema validation", function(){
            pm.response.to.have.jsonSchema(exp_schema_500)
        });

        break;
    default:
        break;
}
```

### 3. Get specific student by id
### Request URL: https://thetestingworldapi.com/api/studentsDetails/{{id}}
### Request Method: GET
#### Response body:
```
{
    "status": "true",
    "data": {
        "id": 10523513,
        "first_name": "Bessie",
        "middle_name": "O'Hara",
        "last_name": "Bruen",
        "date_of_birth": "1977-6-18"
    }
}
```
#### Post-response scripts:
```
var jsonData = pm.response.json();
var status_code = pm.response.code;
//console.log(status_code)

switch(status_code){
    case 200:
        pm.test("Status Code 200",)
    
        pm.test("Verify First Name", function () {
            pm.expect(jsonData.data.first_name).to.eql(pm.environment.get("first_name"));
        });

        pm.test("Varify Middle name", function(){
            pm.expect(jsonData.data.middle_name).to.eql(pm.environment.get("middle_name"))
        });

        pm.test("Varify Last name", function(){
            pm.expect(jsonData.data.last_name).to.eql(pm.environment.get("last_name"))
        });

        pm.test("Verify Date of Birth", function(){
            pm.expect(jsonData.data.date_of_birth).to.eql(pm.environment.get("date_of_birth"));
        });

        //----------------- Json schema verify
        var expectedJsonSchema = 
        {
        "type": "object",
        "properties": {
            "status": {
            "type": "string"
            },
            "data": {
            "type": "object",
            "properties": {
                "id": {
                "type": "integer"
                },
                "first_name": {
                "type": "string"
                },
                "middle_name": {
                "type": "string"
                },
                "last_name": {
                "type": "string"
                },
                "date_of_birth": {
                "type": "string"
                }
            },
            "required": [
                "id",
                "first_name",
                "middle_name",
                "last_name",
                "date_of_birth"
            ]
            }
        },
        "required": [
            "status",
            "data"
        ]
        };

        pm.test("Verify Json Schema", function(){
            pm.response.to.have.jsonSchema(expectedJsonSchema);
        });
        
        break;
    
    case 400:
        pm.test("Verify Status code: 400");
        pm.test("Verify Status Message", function(){
            pm.expect(pm.response.status).to.eql("Bad Request");
        })
        break;

    case 404:
        pm.test("Verify Status code: 404");
        pm.test("Verify Status Message", function(){
            pm.expect(pm.response.status).to.eql("Not Found");
        })
        break;

    case 500:
        pm.test("Verify Status code; 500");
        pm.test("Verify Status Message", function(){
            pm.expect(pm.response.status).to.eql("Internal Server Error");
        })
        break;
    default:
        pm.test("Something went wrong...!! ");
        break;
}
```
### 4. Create Technical Skill
### Request URL: https://thetestingworldapi.com/api/technicalskills
### Request Method: POST
#### Pre-request Script:
```
// select 3 languages randomly
function select_language(all_languages){
    var language =[];
    for(i = 0; i<3; i++){
        var random = Math.floor(Math.random()*8);
        language[i] = all_languages[random];
    }
    if(language[0] != language[1] && language[0] != language[2] && language[1] != language[2]){
        pm.environment.set("languages", language);
        return;
    }
    select_language(all_languages);
}
var all_languages = ["Java", "Python", "Java Script", "Rubi", "Rust", "C", "C++", "Bash","PHP"];

select_language(all_languages);

// year of experience (1-10)
var year_exp = Math.floor(Math.random()*10)+1;
pm.environment.set("year_exp", year_exp.toString());

// last used
var last_used = Math.floor(Math.random()*5)+1;
pm.environment.set("last used", last_used.toString());
```
#### Request body:
```
{ 
"id": 1, 
"language": [ 
"{{languages}}"
], 
"yearexp": "{{year_exp}}", 
"lastused": "{{last used}}", 
"st_id": {{id}} 
}
```
#### Response body:
```
{
    "status": "true",
    "msg": "Add  data success"
}
```
#### Post-response scripts:
```
const status_code = pm.response.code;
const jsonData = pm.response.json();

switch(status_code){
    case 200:
        pm.test("Verify Status Code: 200");
                // response body status
        pm.test("Verify Response Body Status", function(){
            pm.expect(jsonData.status).to.eql("true");
        });
                // response body message
        pm.test("Verify Response Body Message", function(){
            pm.expect(jsonData.msg).to.eql("Add  data success");
        });
            // response schema verify
        const expected_schema =
            {
                "type": "object",
                "properties": {
                    "status": {
                    "type": "string"
                    },
                    "msg": {
                    "type": "string"
                    }
                },
                "required": [
                    "status",
                    "msg"
                ]
            };
        pm.test("Response Schema Verify", function(){
            pm.response.to.have.jsonSchema(expected_schema);
        });
        break;

   case 400:
        pm.test("Verify Status code: 400");
        pm.test("Verify Status Message", function(){
            pm.expect(pm.response.status).to.eql("Bad Request");
        })
        break;

    case 404:
        pm.test("Verify Status code: 404");
        pm.test("Verify Status Message", function(){
            pm.expect(pm.response.status).to.eql("Not Found");
        })
        break;

    case 500:
        pm.test("Verify Status code: 500");
        pm.test("Verify Status Message", function(){
            pm.expect(pm.response.status).to.eql("Internal Server Error");
        })
        break;
    default:
        pm.test("Something went wrong...!! ");
        break;
};
```

### 5. Create student address
#### Request URL: https://thetestingworldapi.com/api/addresses
#### Request Method: POST
#### Pre-request scripts:
```
// house number
var house_number = pm.variables.replaceIn("{{$randomInt}}");
pm.environment.set("house_number",house_number);

// city
var city = pm.variables.replaceIn("{{$randomCity}}");
pm.environment.set("city",city.toString());

//state 
var states = ["New York", "Claifornia", "Bronx", "Miami","Texas", "Florida", "Ohio", "Hawaii"];
var temp = Math.floor(Math.random() * 8);
var state = states[temp];
pm.environment.set("state",state);

// country
var country = pm.variables.replaceIn("{{$randomCountry}}");
pm.environment.set("country",country);

// phone number
var mobile = pm.variables.replaceIn("{{$randomPhoneNumber}}");
pm.environment.set("mobile",mobile);

var std_code = pm.variables.replaceIn("{{$randomInt}}");
pm.environment.set("std_code", std_code.toString());

var home = pm.variables.replaceIn("{{$randomPhoneNumber}}");
pm.environment.set("home",home);
```
#### Request body:
```
{ 
"Permanent_Address": { 
"House_Number": "{{house_number}}",
"City": "{{city}}",
 "State": "{{state}}", 
"Country": "{{country}}",
"PhoneNumber": [ 
{ 
"Std_Code": "{{std_code}}",
"Home": "{{home}}",
"Mobile": "{{mobile}}" 
}
] 
},
"stId": {{id}} 
}
```
#### Response body:
```
{
    "status": "true",
    "msg": "Add  data success"
}
```
#### Post-response script:
```
const status_code = pm.response.code;
const jsonData = pm.response.json();

switch (status_code) {
    case 200:
        //  Verify status code and messages 
        pm.test("Verify Status Code: 200");
        pm.test("Verify Status Message", function(){
            pm.expect(pm.response.status).to.eql("OK");
        });
        // response body status
        pm.test("Verify Response Body Status", function () {
            pm.expect(jsonData.status).to.eql("true");
        });
        // response body message
        pm.test("Verify Response Body Message", function () {
            pm.expect(jsonData.msg).to.eql("Add  data success");
        });
        // response schema verify
        const expected_schema =
        {
            "type": "object",
            "properties": {
                "status": {
                    "type": "string"
                },
                "msg": {
                    "type": "string"
                }
            },
            "required": [
                "status",
                "msg"
            ]
        };
        pm.test("Response Schema Verify", function () {
            pm.response.to.have.jsonSchema(expected_schema);
        });
        break;

    case 400:
        pm.test("Verify Status code: 400");
        pm.test("Verify Status Message", function(){
            pm.expect(pm.response.status).to.eql("Bad Request");
        })
        break;

    case 404:
        pm.test("Verify Status code: 404");
        pm.test("Verify Status Message", function(){
            pm.expect(pm.response.status).to.eql("Not Found");
        })
        break;

    case 500:
        pm.test("Verify Status code; 500");
        pm.test("Verify Status Message", function(){
            pm.expect(pm.response.status).to.eql("Internal Server Error");
        })
        break;
    default:
        pm.test("Something went wrong...!!");
        break;
};
```
### 6. Get address by id
#### Request URL: https://thetestingworldapi.com/api/addresses/{{id}}
#### Request Method: GET
#### Response body:
```
[
    {
        "Permanent_Address": {
            "House_Number": "446",
            "City": "New Marjoryfort",
            "State": "New York",
            "Country": "Fiji",
            "PhoneNumber": [
                {
                    "Std_Code": "158",
                    "Home": "545-317-0491",
                    "Mobile": "316-839-2128"
                }
            ]
        },
        "Current_Address": null,
        "stId": "10523513"
    }
]
```
#### Post-response script:
```
var jsonData  = pm.response.json();
var status_code = pm.response.code;

switch(status_code){
    case 200:
        pm.test("Verify Status code is 200");
        pm.test("Verify Status message:OK",function(){
            pm.expect(pm.response.status).to.eql("OK");
        });

        /*
         since there can be multiple address,
         we will check the last one because
         environment stores the last created values
        */
        var checkLastOne = jsonData.length - 1; 
        pm.test("Verify House number", function(){
            pm.expect(jsonData[checkLastOne].Permanent_Address.House_Number).to.eql(pm.environment.get("house_number"));
            });

        pm.test("Verify City", function(){
            pm.expect(jsonData[checkLastOne].Permanent_Address.City).to.eql(pm.environment.get("city"));
            });

        pm.test("Verify State", function(){
            pm.expect(jsonData[checkLastOne].Permanent_Address.State).to.eql(pm.environment.get("state"));
            });

        pm.test("Verify Country", function(){
            pm.expect(jsonData[checkLastOne].Permanent_Address.Country).to.eql(pm.environment.get("country"));
            });    
        pm.test("Verify Std code of phone number", function(){
            pm.expect(jsonData[checkLastOne].Permanent_Address.PhoneNumber[0].Std_Code).to.eql(pm.environment.get("std_code"));
            }); 

        pm.test("Verify Home number", function(){
            pm.expect(jsonData[checkLastOne].Permanent_Address.PhoneNumber[0].Home).to.eql(pm.environment.get("home"));
            });
        
        pm.test("Verify mobile number", function(){
            pm.expect(jsonData[checkLastOne].Permanent_Address.PhoneNumber[0].Mobile).to.eql(pm.environment.get("mobile"));
            });
        
        break;
    
    case 400:
        pm.test("Verify Status code: 400");
        pm.test("Verify Status Message", function(){
            pm.expect(pm.response.status).to.eql("Bad Request");
        })
        break;

    case 404:
        pm.test("Verify Status code: 404");
        pm.test("Verify Status Message", function(){
            pm.expect(pm.response.status).to.eql("Not Found");
        })
        break;

    case 500:
        pm.test("Verify Status code is 500");
        pm.test("Verify Status Message", function(){
            pm.expect(pm.response.status).to.eql("Internal Server Error");
        })
        break;

    default:
        pm.test("Unexpected Status Code: " + status_code, function() {
            pm.expect(false).to.be.true;  // Force failure if status is not 200
        });

}
```
### 7. Final student details
#### Request URL: https://thetestingworldapi.comapi/FinalStudentDetails/{{id}}
#### Request Method: GET
#### Response body:
```
{
    "status": "true",
    "data": {
        "first_name": "Bessie",
        "middle_name": "O'Hara",
        "last_name": "Bruen",
        "date_of_birth": "1977-6-18",
        "TechnicalDetails": [
            {
                "id": 801428,
                "language": [
                    "C++",
                    "Bash",
                    "Java Script"
                ],
                "yearexp": "4",
                "lastused": "1",
                "st_id": "10523513"
            }
        ],
        "Address": []
    }
}
```
#### Post-response script:
```
var status_code = pm.response.code;

switch(status_code){
    case 200:
        pm.test("Verify Status Code 200");

        pm.test("Verify Status Message", function () {
            var status = pm.response.status;
            pm.expect(status).to.eql("OK");
        });

        var jsonData = pm.response.json();
        pm.test("Verify Languages",function(){
            var env_languages = pm.environment.get("languages");
            console.log(env_languages);
            var flag = 0;
            
            for(var i=0; i<jsonData.data.TechnicalDetails.length; i++){
                var check = 0;
                for(var j=0; j<env_languages.length; j++){
                    if(jsonData.data.TechnicalDetails[i].language[j] === env_languages[j]){
                        check++;
                    }
                }
                if(check === env_languages.length){
                    flag = 1;
                    break;
                }
            }
            pm.expect(flag).to.equal(1);
        });

        pm.test("Verify Year Of Experience", function(){
            var exp = pm.environment.get("year_exp");
            var flag = 0;
            for(var i=0; i<jsonData.data.TechnicalDetails.length; i++){
                if(jsonData.data.TechnicalDetails[i].yearexp === exp){
                    flag=1;
                    break;
                }
            }
            pm.expect(flag).to.equal(1);
        })

        pm.test("Verify House Number", function(){
            var flag = 0;
            var house_num = pm.environment.get("house_number");
            for(var i=0; i<jsonData.data.Address.length; i++){
                if(jsonData.data.Address[i].Permanent_Address.House_Number === house_num){
                    flag = 1;
                    break;
                }
            }
            pm.expect(flag).to.equal(1);
        })

        pm.test("Verify City", function(){
            var flag = 0;
            var city = pm.environment.get("city");
            for(var i=0; i<jsonData.data.Address.length; i++){
                if(jsonData.data.Address[i].Permanent_Address.City === city){
                    flag = 1;
                    break;
                }
            }
            pm.expect(flag).to.equal(1);
        })

        pm.test("Verify Country", function(){
            var flag = 0;
            var country = pm.environment.get("country");
            for(var i=0; i<jsonData.data.Address.length; i++){
                if(jsonData.data.Address[i].Permanent_Address.Country === country){
                    flag = 1;
                    break;
                }
            }
            pm.expect(flag).to.equal(1);
        })

        pm.test("Verify Mobile", function(){
            var flag = 0;
            for(var i=0; i<jsonData.data.Address.length; i++){
                if(jsonData.data.Address[i].Permanent_Address.PhoneNumber[0].Mobile === pm.environment.get("mobile")){
                    flag = 1;
                    break;
                }
            }
            pm.expect(flag).to.equal(1);
        })

        pm.test("Verify Current Address", function(){
            var flag = 0;
            for(var i=0; i<jsonData.data.Address.length; i++){
                if(jsonData.data.Address[i].Current_Address === null ){
                    flag = 1;
                    break;
                }
            }
            pm.expect(flag).to.equal(1);
        })

        // pm.test("Verify response time is less than 500ms", function () {
        //     pm.expect(pm.response.responseTime).to.be.below(500);
        // });


        // ------------- Schema check

        const expectedJsonSchema =
        {
        "type": "object",
        "properties": {
            "status": {
            "type": "string"
            },
            "data": {
            "type": "object",
            "properties": {
                "first_name": {
                "type": "string"
                },
                "middle_name": {
                "type": "string"
                },
                "last_name": {
                "type": "string"
                },
                "date_of_birth": {
                "type": "string"
                },
                "TechnicalDetails": {
                "type": "array",
                "items": [
                    {
                    "type": "object",
                    "properties": {
                        "id": {
                        "type": "integer"
                        },
                        "language": {
                        "type": "array",
                        "items": [
                            {
                            "type": "string"
                            },
                            {
                            "type": "string"
                            },
                            {
                            "type": "string"
                            }
                        ]
                        },
                        "yearexp": {
                        "type": "string"
                        },
                        "lastused": {
                        "type": "string"
                        },
                        "st_id": {
                        "type": "string"
                        }
                    },
                    "required": [
                        "id",
                        "language",
                        "yearexp",
                        "lastused",
                        "st_id"
                    ]
                    }
                ]
                },
                "Address": {
                "type": "array",
                "items": [
                    {
                    "type": "object",
                    "properties": {
                        "Permanent_Address": {
                        "type": "object",
                        "properties": {
                            "House_Number": {
                            "type": "string"
                            },
                            "City": {
                            "type": "string"
                            },
                            "State": {
                            "type": "string"
                            },
                            "Country": {
                            "type": "string"
                            },
                            "PhoneNumber": {
                            "type": "array",
                            "items": [
                                {
                                "type": "object",
                                "properties": {
                                    "Std_Code": {
                                    "type": "string"
                                    },
                                    "Home": {
                                    "type": "string"
                                    },
                                    "Mobile": {
                                    "type": "string"
                                    }
                                },
                                "required": [
                                    "Std_Code",
                                    "Home",
                                    "Mobile"
                                ]
                                }
                            ]
                            }
                        },
                        "required": [
                            "House_Number",
                            "City",
                            "State",
                            "Country",
                            "PhoneNumber"
                        ]
                        },
                        "Current_Address": {
                        "type": "null"
                        },
                        "stId": {
                        "type": "string"
                        }
                    },
                    "required": [
                        "Permanent_Address",
                        "Current_Address",
                        "stId"
                    ]
                    }
                ]
                }
            },
            "required": [
                "first_name",
                "middle_name",
                "last_name",
                "date_of_birth",
                "TechnicalDetails",
                "Address"
            ]
            }
        },
        "required": [
            "status",
            "data"
        ]
        };

        pm.test("Verify Json Schema", function(){
            pm.response.to.have.jsonSchema(expectedJsonSchema);
        })

        break;

    case 400:
        pm.test("Verify Status code 400");

        pm.test("Verify Status Message", function () {
            var status = pm.response.status;
            pm.expect(status).to.eql("Bad Request");
        });
        break;
    
    case 404:
        pm.test("Verify Status code 404");

        pm.test("Verify Status Message", function () {
            var status = pm.response.status;
            pm.expect(status).to.eql("Not Found");
        });
        break;

    case 500:
        pm.test("Verify staus code 500: Internal Server Error");

        pm.test("Verify Status Message", function () {
            var status = pm.response.status;
            pm.expect(status).to.eql("Internal Server Error");
        });
        break;
    
    default:
        pm.test("Something went worng...!!!");
        break;
}
```
### 8. Delete
#### Request URL: https://thetestingworldapi.com/api/studentsDetails/{{id}}
#### Request Method: DELETE
#### Response body:
```
{
    "status": "true",
    "msg": "Delete  data success"
}
```
#### Post-response script:
```
var jsonData = pm.response.json();
pm.test("Verify status code is 200",function(){
    pm.expect(pm.response.code).to.equal(200);
});
pm.test("Verify status message is OK",function(){
    pm.expect(pm.response.status).eql("OK");
});

pm.test("Verify response status",function(){
    pm.expect(jsonData.status).eql("true")
});
pm.test("Verify response message",function(){
    pm.expect(jsonData.msg.trim()).eql("Delete  data success")
});

const expSchema = {
  "type": "object",
  "properties": {
    "status": {
      "type": "string"
    },
    "msg": {
      "type": "string"
    }
  },
  "required": [
    "status",
    "msg"
  ]
}

pm.test("Verify response json schema",function(){
    pm.response.to.have.jsonSchema(expSchema);
})
```
### 9. Get deleted student
#### Request URL: https://thetestingworldapi.com/api/FinalStudentDetails/{{id}}
#### Request Method: GET
#### Response body:
```
{
    "status": "false",
    "msg": "No File Found"
}
```
#### Post-response scripts:
```
const jsonData = pm.response.json()

pm.test("Verify Status code: 404", function(){
	pm.response.to.have.status(404)
});

pm.test("Verify Status Message", function(){
	pm.expect(pm.response.status).to.eql('Not Found')
})
pm.test("Verify Response body status", function(){
	pm.expect(jsonData.status).to.eql("false");
})

pm.test("Verify Response body message", function(){
	pm.expect(jsonData.msg).to.eql("No File Found");
})
```

## Run Command:
- ### Run Command for Console:
```
newman run Shobuj-Das_Testing_World_API.postman_collection.json -e Shobuj-Das_Testing_World_API.postman_environment.json
```
- ### Run Command for Report:
```
newman run Shobuj-Das_Testing_World_API.postman_collection.json -e Shobuj-Das_Testing_World_API.postman_environment.json -r cli,htmlextra
```

## Newman Report Summary:

![image](https://github.com/user-attachments/assets/f8a99b37-a574-475e-9428-a1b227d4d616)
![image](https://github.com/user-attachments/assets/3ae35000-e368-427f-8fc9-a11ea1fb6e49)
![image](https://github.com/user-attachments/assets/32fd6895-c28d-484a-bf3c-1c0f227428e1)
![image](https://github.com/user-attachments/assets/6ccbed73-71d8-411b-81ea-b1aa2846ffe6)


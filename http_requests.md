# HTTP testing in VSCode

#
#http #REST #testing
#

### Setting Up REST Client Environment in VSCode

```
"rest-client.environmentVariables": {
        "MyEnvironment": {
            "apikey": "myapikey",
            "username":"myusername",
            "password":"mypassword"
        }
}
```
### Creating test files
- Create **Curl / REST API** invocation files with extension **.http** or **.curl**

- In those files you can define variables like this:
    
    `@myvar = value`

- Then you can write **Curl / REST** commands like this

    ```
    curl {{server_url}}/v1/skills \
        -X POST \
        -H "Content-Type:application/json" \ 
        -H "Accept: application/json" \
        -d @test-data/api_only/newSkill.json
    ```
-   Here `{{server_url}}` is referring to a **variable server_url** you’ve define either in the **file** or in **environment.**

    And the data to be posted is in file `newSkill.json`

### Example
And here’s a sample when you may want to consume the output of one of the curl commands as input in subsequent commands:

```
### CREATE NEW PROCESS SKILL
# @name createProcessSkill
curl {{server_url}}/v1/skills \
    -X POST \
    -H "Content-Type:application/json" \ 
    -H "Accept: application/json" \
    -d @test-data/api_only/newProcessSkill.json

### ADD PROCESS
@processSkillId = {{createProcessSkill.response.body.id}}
curl {{server_url}}/v1/skills/{{processSkillId}}/processes \
    -X POST \
    -H "Content-Type:application/json" \
    -H "Accept: application/json" \
    -d @test-data/api_only/CookingRiceBPMN.json
```
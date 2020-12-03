## Daily Practice
### Thursday, December 3rd, 2020
---


# [Playing With PHP](https://laracasts.com/series/playing-with-php)


## [The Hue REST API](https://laracasts.com/series/playing-with-php/episodes/1)

Need to find IP address of Hue bridge  
- IP: `192.168.1.4`
- MAC Address: `EC:B5:FA:11:75:43`

Test app located at: [https://192.168.1.4/debug/clip.html](https://192.168.1.4/debug/clip.html)
- REST client for lights

### Get a username to use the API

`POST` request to `/api`:  
```
{
    "devicetype":"condo_lights#work_mb dj"
}
``` 

After sending this request, need to press the physical button on the Hue bridge to authenticate, and re-send the request  

Response:
```
[
	{
		"success": {
			"username": "km4AnsCcdbDtnyIcdr2Pt86uAWfWfF5bhWNPc1sA"
		}
	}
]
```

Now, use this username to make requests to the API: `/api/km4AnsCcdbDtnyIcdr2Pt86uAWfWfF5bhWNPc1sA/...`

### Get information about lights

Each light has its own URL  
See information for all lights: `GET` request to `<bridge ip address>/api/<username>/lights`  
- in my case, `GET` request to `https://192.168.1.4/api/km4AnsCcdbDtnyIcdr2Pt86uAWfWfF5bhWNPc1sA/lights`

Response (just for the bulb at my desk):
```
{
    ...

    "9": 
        {
            "state": {
                "on": true,
                "bri": 254,
                "hue": 34076,
                "sat": 251,
                "effect": "none",
                "xy": [
                    0.3144,
                    0.3301
                ],
                "ct": 153,
                "alert": "select",
                "colormode": "xy",
                "mode": "homeautomation",
                "reachable": true
            },
            "swupdate": {
                "state": "noupdates",
                "lastinstall": "2020-11-22T19:13:20"
            },
            "type": "Extended color light",
            "name": "DJ’s Desk",
            "modelid": "LCT001",
            "manufacturername": "Signify Netherlands B.V.",
            "productname": "Hue color lamp",
            "capabilities": {
                "certified": true,
                "control": {
                    "mindimlevel": 5000,
                    "maxlumen": 600,
                    "colorgamuttype": "B",
                    "colorgamut": [
                        [
                            0.675,
                            0.322
                        ],
                        [
                            0.409,
                            0.518
                        ],
                        [
                            0.167,
                            0.04
                        ]
                    ],
                    "ct": {
                        "min": 153,
                        "max": 500
                    }
                },
                "streaming": {
                    "renderer": true,
                    "proxy": false
                }
            },
            "config": {
                "archetype": "sultanbulb",
                "function": "mixed",
                "direction": "omnidirectional",
                "startup": {
                    "mode": "safety",
                    "configured": true
                }
            },
            "uniqueid": "00:17:88:01:00:fa:ad:19-0b",
            "swversion": "5.127.1.26581"
        }
}
```

To get information about __only__ the bulb on my desk (Bulb #9): `GET` request to `https://192.168.1.4/api/km4AnsCcdbDtnyIcdr2Pt86uAWfWfF5bhWNPc1sA/lights/9`  

To get information about groups: `/groups`  


### Turn a light on/off

A single bulb:  
`PUT` request to `/lights/<light id>/state` with the fields to be updated in the body  
- for the bulb at my desk, `PUT` request to `https://192.168.1.4/api/km4AnsCcdbDtnyIcdr2Pt86uAWfWfF5bhWNPc1sA/lights/9/state`
- body: `{"on": false}`

A group of bulbs:
`PUT` request to `/groups/<group id>/action` with the fields to be updated in the body  
- for my "loft" group, `PUT` request to `https://192.168.1.4/api/km4AnsCcdbDtnyIcdr2Pt86uAWfWfF5bhWNPc1sA/groups/9/action`
- body: `{"on": false}`  


### Change color

A single bulb:  
`PUT` request to `/lights/<light id>/state` with the fields to be updated in the body  
- for the bulb at my desk, `PUT` request to `https://192.168.1.4/api/km4AnsCcdbDtnyIcdr2Pt86uAWfWfF5bhWNPc1sA/lights/9/state`
- body:  
   ```
   {
       "on": true,
       "sat": 254,
       "bri": 254,
       "hue": 10000
   }
   ```
   - `"sat"` = saturation/intensity, 0-254
   - `"bri"` = brightness, 0-254
   - `"hue"` = measure of color, 0-65535



## [Build a Client App Using Vue](https://laracasts.com/series/playing-with-php/episodes/2)

Using `axios` to make API requests - turning a bulb on:  
```
axios.put('https://192.168.1.4/api/km4AnsCcdbDtnyIcdr2Pt86uAWfWfF5bhWNPc1sA/lights/9/state', {
    on: true,
}).then(response => {

}).catch(error => {
    console.log(error.response);
});
```

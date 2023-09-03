This one's a fun one, but took a lot of trial and error for me.

The link we're given is a RESTful API spec. All we need to do is follow the spec and make multiple queries to eventually pinpoint the suburb of our target.

At minimum we need to have in our request body the values mentioned in the example:
```json
{
  "device": {
    "phoneNumber": "string"
  },
  "area": {
    "areaType": "Circle",
    "center": {
      "latitude": 0,
      "longitude": 0
    },
    "radius": 0
  },
  "maxAge": 120
}
```
Phone number is given to us, and our starting point is somewhere in Victoria Australia.
Lat/Longitudes are obviously GPS coordinates - these can be obtained via right clicking any point on google maps.

For radius, the spec mentions the range as:  `radius: integer [2000, 200000]`. Looking at the scale guide while zooming in on google maps, it's likely that the units of radius is in meters.

A useful tool also available to us is the Measure Distance tool in google maps. When we pin a point, we can right click and select "Measure Distance" to pop out a ruler, which we can use to draft our circle zones as we pinpoint our target phone.

To actually make the request to this API, I used `curl`. More specifically, the command structure is something like this:
```bash
curl -X POST -H "Content-Type: application/json" \
    -d '{"device": {
        "phoneNumber":"+61491578888"
    },
    
  "area": {
    "areaType": "Circle",
    "center": {
      "latitude": -36.44994685882602, 
      "longitude":  146.42916954026174
    },
    "radius": 3000
  },
  "maxAge": 120
}' \
    https://osint-faraday-9e36cbd6acad.2023.ductf.dev/verify
```
(Note: this is the final request I landed on before submitting the flag.)

The methodology I used is as follows:
1. Start with a circle of max radius (200km)
2. Pick a point (e.g. Melbourne), and 
	1. If unsuccessful, use measure distance tool to observe the exclusion zone, and pick another point of interest outside it, but also within the successful zone radius (if known)
	2. If successful, half radius and ping it again using the same coordinates
3. Repeat step 2.

Eventually landed on Milawa, so that's the flag

Flag: `DUCTF{milawa}`
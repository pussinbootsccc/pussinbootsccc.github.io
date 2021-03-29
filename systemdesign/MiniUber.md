---
layout: page
title: Mini Uber
permalink: /systemdesign/miniuber
---
You can implement it with the following instructions:
{% highlight txt %}
report(driver_id, lat, lng)
    return null if no matched rider.
    return matched trip information.

request(rider_id, lat, lng)
    create a trip with rider's information.
    find a closest driver, mark this driver not available.
    fill driver_id into this trip.
    return trip
{% endhighlight %} 

{% highlight txt %}
Input:
  report(1, 36.1344, 77.5672) 
  report(2, 45.1344, 76.5672) 
  request(2, 39.1344, 76.5672) 
  report(1, 38.1344, 75.5672) 
  report(2, 45.1344, 76.5672) 
Output:
  null
  null
  Trip(rider_id: 2, driver_id: 1, lat: 39.1344, lng: 76.5672)
  Trip(rider_id: 2, driver_id: 1, lat: 39.1344, lng: 76.5672)
  null
{% endhighlight %} 

Key Ideas
- maintain `locations` map, a free driver : location
- maintain `trips` map, a busy driver : trip
- two maps keys should be mutually exclusive

{% highlight java %}
public class MiniUber {
    private Map<Integer, Trip> trips;
    private Map<Integer, Location> locations;

    public MiniUber() {
        this.trips = new HashMap<>(); // a busy driverId : trip
        this.locations = new HashMap<>(); // a free driverId: location
    }

    public Trip report(int driverId, double lat, double lng) {
        if (trips.containsKey(driverId)) { // driver with a trip, report the trip
            return trips.get(driverId);
        }
        Location record = locations.get(driverId); // drive with no trip, update location, report null
        if (record == null) { // create location
            locations.put(driverId, new Location(lat, lng));
        } else { // update old location
            record.lat = lat;
            record.lng = lng;
        }
        return null;
    }

    public Trip request(int riderId, double lat, double lng) {
        Trip trip = new Trip(riderId, lat, lng); // customerId, pick location
        double minDistance = Integer.MAX_VALUE;
        int driverId = -1;
        // find free and nearest driver id if any
        for (Map.Entry<Integer, Location> entry : locations.entrySet()) {
            Location location = entry.getValue();
            double distance = Helper.getDistance(location.lat, location.lng, lat, lng);
            if (distance < minDistance) {
                driverId = entry.getKey();
                minDistance = distance;
            }
        }
        if (driverId != -1) { // find the driver
            locations.remove(driverId); // driver not free, remove from locations map
            trip.driverId = driverId; // complete this trip's info
            trips.put(driverId, trip); // put onto trips map
        }
        return trip;
    }
}

class Helper {
    public static double getDistance(double lat1, double lng1, double lat2, double lng2) {
        return 0.0; // a placeholder value;
    }
}

class Trip {
    public int id; // trip's id, primary key
    public int driverId, riderId; // foreign key
    public double lat, lng; // customer pick up location
    public Trip(int riderId, double lat, double lng) {
        this.riderId = riderId;
        this.lat = lat;
        this.lng = lng;
    }
}

class Location {
    double lat;
    double lng;
    Location(double lat, double lng) {
        this.lat = lat;
        this.lng = lng;
    }
}

{% endhighlight %}
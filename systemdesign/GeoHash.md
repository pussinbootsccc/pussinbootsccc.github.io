---
layout: page
title: Geo Hash
permalink: /systemdesign/geohash
---
Geohash is a hash function that convert a location coordinate pair into a base32 string.   
The task is to convert (latitude, longitude) pair into a geohash string.  

{% highlight txt %}
Input: 
lat = 39.92816697 
lng = 116.38954991
precision = 12 

Output:
"wx4g0s8q3jf9"
{% endhighlight %} 

Key Ideas:
- make double lag, lng value into a 30-bit binary representation string
    - compute `mid` pivot
        - value in left half, append `0`
        - value in right half, append `1`
        - e.g. `getBinary(39.92816697, -90, 90)` = `"101110001100100101100110111001"`  
    - reduce search size by moving `left`, `right` via `mid`
- mix lag, lng binary into a 60-bit binary String
- encode every 5-bit using base32 to a char, to make a 12 length string

Notes:
- the way to hash a double value to retain its uniqueness
    - use a binary representation given a range
    - granularity controlled by rounds of appending a bit


{% highlight java %}
public class GeoHash {
    private static final String base32 = "0123456789bcdefghjkmnpqrstuvwxyz";
    private static final int bitLen = 30;

    public String encode(double latitude, double longitude, int precision) {
        String latitudeBinary = getBinary(latitude, -90, 90);
        String longitudeBinary = getBinary(longitude, -180, 180);

        StringBuilder mixed = new StringBuilder();
        for (int i = 0; i < bitLen; i++) { // create mix 60-bit binary with 30-bit from each
            mixed.append(longitudeBinary.charAt(i));
            mixed.append(latitudeBinary.charAt(i));
        }
        StringBuilder hash = new StringBuilder();
        for (int i = 0; i < mixed.length(); i += 5) { // every 5-bit to be encoded as a char
            int index = binaryToInt(mixed.substring(i, i + 5));
            hash.append(base32.charAt(index));
        }
        return hash.substring(0, precision);
    }

    private int binaryToInt(String binary) {
        return Integer.parseInt(binary, 2);
    }

    private String getBinary(double value, double left, double right) {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < bitLen; i++) { // generate a 30-bit long string
            double mid = (left + right) / 2.0;
            if (value <= mid) { // value fall in left half, use 0 to represent
                sb.append("0");
                right = mid;
            } else {           // value fall in right side, use 1 to represent
                sb.append("1");
                left = mid;
            }
        }
        return sb.toString();
    }
}
{% endhighlight %}
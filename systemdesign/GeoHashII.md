---
layout: page
title: Geo Hash II
permalink: /systemdesign/geohashii
---
Decode geohash string to the original latitude and longitude.
{% highlight txt %}
Input:
"wx4g0s"

Output: 
lat = 39.92706299
lng = 116.39465332
{% endhighlight %} 

Key Ideas
- `geohash` literal string to `mixed` binary string
- `mixed` binary string to `lag`, `lng` binary string
- decode lag, lng binary to original double values

Notes  
- `intToBinary()`, to generate from right to left.  
- `getGeo()`, to narrow range, then formulate a closer value.  

{% highlight java %}
public class GeoHashII {
    private static final String base32 = "0123456789bcdefghjkmnpqrstuvwxyz";

    public double[] decode(String geohash) {
        int n = geohash.length();
        StringBuilder mixed = new StringBuilder(); // mixed binary string of lat & lng
        for (int i = 0; i < n; i++) {
            int val = base32.indexOf(geohash.charAt(i)); // find index of char in base
            mixed.append(intToBinary(val)); // append 5-len binary string
        }

        StringBuilder lng = new StringBuilder();
        StringBuilder lat = new StringBuilder();

        for (int i = 0; i < mixed.length(); i++) { // get lngBinary, latBinary respectively
            if (i % 2 == 0) {
                lng.append(mixed.charAt(i));
            } else {
                lat.append(mixed.charAt(i));
            }
        }

        double[] res = new double[2];
        res[0] = getGeo(lat.toString(), -90, 90);
        res[1] = getGeo(lng.toString(), -180, 180);
        return res;
    }

    private double getGeo(String bin, double left, double right) { // decode bin string to double value
        double res = 0.0;
        for (int i = 0; i < bin.length(); i++) {
            double mid = (left + right) / 2;
            if (bin.charAt(i) == '0') {
                right = mid;
            } else {
                left = mid;
            }
            res = (left + right) / 2.0; // try formulate real value a bit closer
        }
        return res;
    }

    private String intToBinary(int value) { // convert to 5-length binary string, 28 => "11100"
        char[] array = new char[5];
        for (int i = 4; i >= 0; i--) { // from low digit to high， right to left
            if (value % 2 == 0) {
                array[i] = '0';
            } else {
                array[i] = '1';
            }
            value /= 2;
        }
        return new String(array);
    }

    public static void main(String[] args) {
        GeoHashII s = new GeoHashII();
        System.out.println(Arrays.toString(s.decode("wx4g0s8q3jf9")));
    }
}
{% endhighlight %}
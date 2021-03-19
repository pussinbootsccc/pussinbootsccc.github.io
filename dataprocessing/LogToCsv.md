---
layout: page
title: Log To CSV
permalink: /dataprocessing/logtocsv
---
given a file with logs to generate a CSV file

{% highlight bash %}
Input:
"""
timestamp="Wed Jun 06 2019 22:00:23" message="test message" id="3" field_4="test 3"
timestamp="Wed Jun 06 2019 22:00:24" message="test" id="4" field_5="test 4" filed_6="test 4"
"""
Output: 
id,timestamp,message,field_1,field_1,field_2,field_3,field_4,field_5,field_6 
3, Wed Jun 06 2019 22:00:23,test,,,,test 3,,
4, Wed Jun 06 2019 22:00:24,test,,,,,test 4,test 4 
{% endhighlight %} 


Key Ideas:
- parse a raw line via linear scan to find all key:value pairs to create an `entry` map.
- given an entry map, base on header's key to build a csv line, if key exists, append, else empty string.
- sort entry list by entry's `id` key


{% highlight java %}
public class LogToCSV {
    private static final String[] headers = {"id", "timestamp", "message" ,"field_1", "field_2", "field_3", "field_4" ,"field_5", "field_6"};
    public void process() {
        String inputPath = "/Users/username/Desktop/logs.txt";
        String outputPath = "/Users/username/Desktop/output.csv";
        List<Map<String, String>> entries = new ArrayList<>();

        // try (resource) will automatically close the br， try-with-resources pattern
        try (BufferedReader br = new BufferedReader(new FileReader(inputPath))) { 
            String line;
            while ((line = br.readLine()) != null) {
                Map<String, String> entry = getEntry(line);
                entries.add(entry);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        // sort entries by id field
        Collections.sort(entries, (x, y) -> Integer.parseInt(x.get("id")) - Integer.parseInt(y.get("id")));

        String headerLine = getHeader();
        try (BufferedWriter bw = new BufferedWriter(new FileWriter(outputPath))) {
            bw.write(headerLine);
            bw.newLine();
            for (Map<String, String> entry : entries) {
                String csvLine = getCsvLine(entry);
                bw.write(csvLine);
                bw.newLine();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private String getHeader() {
        String header = String.join(",", headers);
        return header;
    }

    private String getCsvLine(Map<String, String> map) {
        StringBuilder sb = new StringBuilder();
        for (String key : headers) {
            sb.append(map.containsKey(key) ? map.get(key) : "").append(",");
        }
        sb.deleteCharAt(sb.length() - 1); // not keep the last ","
        return sb.toString();
    }

    // timestamp="Wed Jun 06 2019 22:00:21" message="test message" id="1" field_1="test 1"
    private Map<String, String> getEntry(String line) {
        Map<String, String> map = new HashMap<>();
        int i = 0;
        int n = line.length();
        while (i < n) {
            int start = i;
            while(i < n && (line.charAt(i) != '=')) {
                i++;
            }
            String key = line.substring(start, i);
            i += 2; // i from =, move 2 steps, points to new letter char

            start = i;
            while(i < n && (line.charAt(i) != '"')) {
                i++;
            }
            String value = line.substring(start, i);
            i += 2; // i from ", move to steps, points to a new letter char

            map.put(key, value);
        }
        return map;
    }

    public static void main(String[] args) {
        LogToCSV s = new LogToCSV();
        s.process();
    }
}
{% endhighlight %}

**JAVA IO**
{% highlight java %}
BufferedReader br = new BufferedReader(new FileReader(inputPath));
String line;
while ((line = br.readLine()) != null) {
  // process line
}
br.close();

BufferedWriter bw = new BufferedWriter(new FileWriter(outputPath));
for (String line : lines) {
    bw.write(line);
    bw.newLine(); // make new line
}
bw.close();
{% endhighlight %}

**JAVA JOIN**
{% highlight java %}
String message = String.join("-", "Java", "is", "best"); // (delimiter, elements...)

String csv = String.join(",", new String[] {"column1", "column2", "column3"});

String[] header = {"column1", "column2", "column3"};
String headerLine = String.join(",", header);
{% endhighlight %}
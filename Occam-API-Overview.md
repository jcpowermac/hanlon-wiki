This guide is intended to be a quickstart for using the Occam REST API. This is not meant to be a comprehensive guide as Occam is still in beta and API elements may change.


## Occam API & REST

Occam provides the ability to expose a [RESTful](http://en.wikipedia.org/wiki/Representational_state_transfer) API through each Slice. Many of the existing Slices provide API access for controlling Occam and its objects.

Occam uses JSON to represent objects rather than XML. At this time XML is not supported.


## HTTP Commands

In REST-style API interfaces: HTTP commands determine the actions performed. Occam API follows this standard with the following commands:

1. GET -  Retrieves an object or set of objects
1. POST - Creates an object or performs a command call
1. PUT - Updates an existing object
1. DELETE - Removes an object

## API URI Path

The URI for an API element follows this format:

http://**{OCCAM API IP}**:**{API PORT}**/occam/api/**{API VERSION}**/**{SLICE NAME}**/**{COMMAND}**/**{COMMAND}**/...

Where:

* **OCCAM API IP** = IP Address for Occam service.
* **API PORT** = Port number for Occam service. Defaults to 8026.
* **API VERSION** = Version number for the Occam API. Currently 'v1'.
* **SLICE NAME** = Name of the Slice you are calling. Examples are: node, model, policy, broker, etc.
* **COMMAND** = Commands to pass to API.

## JSON Response

The format of an API response is consistent between most Slices. An example JSON response for getting all nodes looks like:

```json
{
    "resource": "ProjectOccam::Slice::Node",
    "command": "nodes_query_all",
    "result": "Ok",
    "http_err_code": 200,
    "errcode": 0,
    "response": [
        {
            "@uuid": "1dKkHnYnI633yuijQfTp2c",
            "@classname": "ProjectOccam::Node",
            "@uri": "http://192.168.99.10:8026/occam/api/v1/node/1dKkHnYnI633yuijQfTp2c"
        },
        {
            "@uuid": "1etJ2JLU0m0xJRYPD2GAyc",
            "@classname": "ProjectOccam::Node",
            "@uri": "http://192.168.99.10:8026/occam/api/v1/node/1etJ2JLU0m0xJRYPD2GAyc"
        },
        {
            "@uuid": "1fs9YAc647Tzxh1RrHACtq",
            "@classname": "ProjectOccam::Node",
            "@uri": "http://192.168.99.10:8026/occam/api/v1/node/1fs9YAc647Tzxh1RrHACtq"
        },
        {
            "@uuid": "1hWZE8LyvLJfxOoDP2tbKY",
            "@classname": "ProjectOccam::Node",
            "@uri": "http://192.168.99.10:8026/occam/api/v1/node/1hWZE8LyvLJfxOoDP2tbKY"
        },
        {
            "@uuid": "1iqntXADkLNDiMy6plT0as",
            "@classname": "ProjectOccam::Node",
            "@uri": "http://192.168.99.10:8026/occam/api/v1/node/1iqntXADkLNDiMy6plT0as"
        }
    ]
}
```

* **resource** = Slice called
* **command** = Command called
* **result** = HTTP Code Response String
* **http\_err\_code** = HTTP Code Response Number
* **errcode** = Occam Internal Error Code
* **response** = JSON Array containing returned Nodes

In a case like above where more than one object is returned. Occam will provide a smaller output with the UUID, Classname, and URI of the Object. You can then use a new HTTP GET request to get the objects you need in full detail. So for the first item in this list we would just need to do a HTTP GET to the URI:

    http://192.168.99.10:8026/occam/api/v1/node/1dKkHnYnI633yuijQfTp2c

The response would look like (truncated for size):

```json
{
    "resource": "ProjectOccam::Slice::Node",
    "command": "get_node_with_uuid",
    "result": "Ok",
    "http_err_code": 200,
    "errcode": 0,
    "response": [
        {
            "@uuid": "5IFPEErRIcfk93tcIBBrD4",
            "@version": 2,
            "@classname": "ProjectOccam::Node",
            "@is_template": false,
            "@hw_id": [
                "0015C5CEB6C9"
            ],
            "@attributes_hash": {
                "mk_hw_nic3_physical_id": "0.1",
                "mk_hw_cpu1_capabilities": "x86-64 fpu fpu_exception wp vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe nx pdpe1gb rdtscp constant_tsc arch_perfmon pebs bts xtopology nonstop_tsc aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 cx16 xtpr pdcm dca sse4_1 sse4_2 popcnt aes lahf_lm ida arat epb tpr_shadow vnmi flexpriority ept vpid cpufreq",
                "mk_hw_lscpu_Vendor_ID": "GenuineIntel",
                "mk_hw_disk4_configuration": "ansiversion=5 signature=000cca69",
                "mk_hw_disk0_size": "931GiB (1TB)",
                "mk_hw_lscpu_CPU_op-modes": "32-bit, 64-bit",
                "mk_hw_nic1_serial": "00:15:c5:ce:b6:c9",
                "mk_hw_cpu0_capacity": "4GHz",
                "mk_hw_disk3_description": "ATA Disk",
                "mk_hw_fw_version": "S5500.86B.01.00.0054.092820101104",
                "memorysize": "2.16 GB",
                "macaddress_dummy0": "A6:0C:D4:E8:50:73",
                "mk_hw_nic3_version": "01",
                "mk_hw_nic0_description": "Ethernet interface",
                "mk_hw_disk5_physical_id": "5",
                "mk_hw_disk1_product": "SAMSUNG HM100UI",
                "mk_hw_lscpu_Stepping": "2",
                "mk_hw_nic1_clock": "33MHz",
                "mk_hw_cpu0_capabilities": "x86-64 boot fpu fpu_exception wp vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe nx pdpe1gb rdtscp constant_tsc arch_perfmon pebs bts xtopology nonstop_tsc aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 cx16 xtpr pdcm dca sse4_1 sse4_2 popcnt aes lahf_lm ida arat epb tpr_shadow vnmi flexpriority ept vpid cpufreq",
                "mk_hw_disk3_bus_info": "scsi@3:0.0.0",
                "mk_hw_fw_capabilities": "pci pnp upgrade shadowing cdboot bootselect edd int13floppy2880 int5printscreen int9keyboard int14serial int10video acpi usb ls120boot zipboot netboot",
                "mk_hw_nic3_clock": "33MHz",
                "mk_hw_nic0_logical_name": "eth1",
                "mk_hw_disk5_version": "045C",
                "mk_hw_disk1_logical_name": "/dev/sdb",
                "mk_hw_lscpu_Virtualization": "VT-x",
                "mk_hw_nic2_description": "Ethernet interface",
                "mk_hw_cpu1_product": "Intel(R) Xeon(R) CPU           E5520  @ 2.26GHz",
                "mk_hw_disk3_serial": "S2GHJ9BB305263",
                "mk_hw_mem_physical_id": "1f",
                "ipaddress_eth1": "192.168.2.42",
                "mk_hw_lscpu_CPU_sockets": "2",
                "mk_hw_nic0_size": "1Gbit/s",
                "mk_hw_disk5_configuration": "ansiversion=5 signature=000cca69",
                "mk_hw_disk1_size": "931GiB (1TB)",
                "mk_hw_lscpu_L2_cache": "256K",
                "mk_hw_nic2_logical_name": "eth0",
                "mk_hw_cpu1_bus_info": "cpu@1",
                "mk_hw_disk_count": "6",
                "netmask_eth1": "255.255.255.0",
                "is_virtual": "false",
                "mk_hw_disk4_description": "ATA Disk",
                "processor0": "Intel(R) Xeon(R) CPU           E5520  @ 2.26GHz",
                "mk_hw_nic0_clock": "33MHz",
                "mk_hw_cpu0_product": "Intel(R) Xeon(R) CPU           E5520  @ 2.26GHz",
                "mk_hw_disk2_product": "SAMSUNG HM100UI",
                "mk_hw_bus_product": "S5520UR",
                "hostname": "mk0015C5CEB6C9",
                "mk_hw_nic2_width": "64 bits",
                "mk_hw_cpu1_slot": "CPU2",
                "mk_hw_disk4_bus_info": "scsi@4:0.0.0",
                "processor3": "Intel(R) Xeon(R) CPU           E5520  @ 2.26GHz",
                "mk_hw_disk0_physical_id": "0",
                "macaddress_eth2": "00:1B:21:87:F8:09",
                "mk_hw_nic1_description": "Ethernet interface",
                "mk_hw_cpu0_bus_info": "cpu@0",
                "mk_hw_bus_serial": "BZUB04203637",
                "mk_hw_disk2_logical_name": "/dev/sdc",
                "mk_hw_nic2_configuration": "autonegotiation=off broadcast=yes driver=ixgbe driverversion=3.3.8-k2 firmware=0.9-3 latency=0 link=no multicast=yes",
                "mk_hw_cpu1_width": "64 bits",
                "mk_hw_disk4_serial": "CVEM042500CJ064KGN",
                "processor6": "Intel(R) Xeon(R) CPU           E5520  @ 2.26GHz",
                "mk_hw_disk0_version": "2AM1",
                "mk_hw_lscpu_Byte_Order": "Little Endian",
                "mk_hw_nic1_logical_name": "eth3",
                "mk_hw_cpu0_slot": "CPU1",
                "mk_hw_disk2_size": "931GiB (1TB)",
                (truncated)
                "mk_hw_cpu0_description": "CPU",
                "mk_hw_disk2_description": "ATA Disk",
                "mk_hw_bus_description": "Motherboard",
                "fqdn": "mk0015C5CEB6C9.admin.lucid.eng.emc.com",
                "mk_hw_nic2_serial": "00:15:c5:ce:b6:c9",
                "mk_hw_cpu1_serial": "0002-06C2-0000-0000-0000-0000",
                "mk_hw_disk4_physical_id": "4",
                "processor2": "Intel(R) Xeon(R) CPU           E5520  @ 2.26GHz",
                "mk_hw_disk0_product": "SAMSUNG HM100UI",
                "ipaddress": "192.168.2.42",
                "mk_hw_nic0_configuration": "autonegotiation=on broadcast=yes driver=igb driverversion=3.0.6-k2 duplex=full firmware=2.1-0 ip=192.168.2.42 latency=0 link=yes multicast=yes port=twisted pair speed=1Gbit/s",
                "mk_hw_cpu0_physical_id": "3a",
                "mk_hw_disk2_bus_info": "scsi@2:0.0.0",
                "mk_hw_bus_version": "E22554-751",
                "mk_hw_nic2_capabilities": "pm msi msix pciexpress bus_master cap_list ethernet physical fibre",
                "mk_hw_cpu1_capacity": "4GHz",
                "processor5": "Intel(R) Xeon(R) CPU           E5520  @ 2.26GHz",
                "mk_hw_disk0_logical_name": "/dev/sda",
                "netmask": "255.255.255.0",
                "mk_hw_disk4_version": "045C",
                "mk_hw_nic1_bus_info": "pci@0000:01:00.1",
                "mk_hw_cpu0_serial": "0002-06C2-0000-0000-0000-0000",
                "mk_hw_disk2_serial": "S2GHJ9BB305270",
                "mk_hw_fw_description": "BIOS",
                "interfaces": "dummy0,eth0,eth1,eth2,eth3,lo",
                "manufacturer": "Dell",
                "productname": "PowerEdge R610"
            },
            "@tags": [
                "nics_4",
                "DellPowerEdge",
                "cpus_2",
                "memsize_32GiB"
            ],
            "@timestamp": 1338312101,
            "@last_state": "idle",
            "@status": "inactive",
            "@uri": "http://192.168.99.10:8026/occam/api/v1/node/5IFPEErRIcfk93tcIBBrD4"
        }
    ]
}
```

A HTTP GET to an object's URI will yield the full object details.

## API Examples

The following sections will illustrate how to call the Occam API using the above HTTP commands. Some of the examples include JSON parsing and some do not.

## GET

### Getting All Objects

To get all objects simple perform a HTTP GET against the slice in question. The examples below are using the Node slice to get Node objects.

Example path:

    /occam/api/v1/node

_Returns all Node objects._

**Ruby**

```ruby
# We use Net::HTTP
require "net/http"
# We create a URI object with our IP, Port, and the path to the Slice (Node)
uri = URI "http://127.0.0.1:8026/occam/api/v1/node"
# We run an HTTP GET against our Node Slice API
res = Net::HTTP.get(uri)
# This returns 
response_hash = JSON.parse(res)
puts response_hash.inspect
```

**Perl**

```perl
# We are using the LWP and JSON modules
use LWP::Simple;
use JSON;
# We run an HTTP GET against our Node Slice API
$contents = get("http://127.0.0.1:8026/occam/api/v1/node");
# Parse the JSON
$json_hash = from_json( $contents );
# Print the HTTP response
print $contents;

```

**Python**

```python
import httplib
import json
conn = httplib.HTTPConnection('127.0.0.1:8026')
conn.request("GET", "/occam/api/v1/node")
resp = conn.getresponse()
body = resp.read()
print json.loads(body)
```

**C#**

```csharp
static string GetNodes() {
  string url = "http://192.168.99.10:8026/occam/api/v1/node";
  HttpWebRequest req = WebRequest.Create(url)
  as HttpWebRequest;
  string result = null;
  using (HttpWebResponse resp = req.GetResponse()
  as HttpWebResponse)
  {
      StreamReader reader =
      new StreamReader(resp.GetResponseStream());
  result = reader.ReadToEnd();
}
return result;
}
```

**Java**

```java
public static String GetNodes() {
  URL url = new URL("http://192.168.99.10:8026/occam/api/v1/node");
  HttpURLConnection conn =
      (HttpURLConnection) url.openConnection();

  BufferedReader rd = new BufferedReader(
      new InputStreamReader(conn.getInputStream()));
  StringBuilder sb = new StringBuilder();
  String line;
  while ((line = rd.readLine()) != null) {
    sb.append(line);
  }
  rd.close();

  conn.disconnect();
  return sb.toString();
}
```

**PowerShell**

_Note:  JSON parsing not shown. [Windows 8 PowerShell V3 includes new JSON cmdlets](http://powershelljson.codeplex.com/)_

```powershell
$api_url = "http://192.168.99.10:8026/occam/api/v1/node"

$request = [System.Net.WebRequest]::Create($api_url)
$request.Method ="GET"
$request.ContentLength = 0

$response = $request.GetResponse()
$reader = new-object System.IO.StreamReader($response.GetResponseStream())
$reader.ReadToEnd()
```

Sample Response (JSON)

```json
{
    "resource": "ProjectOccam::Slice::Node",
    "command": "nodes_query_all",
    "result": "Ok",
    "http_err_code": 200,
    "errcode": 0,
    "response": [
        {
            "@uuid": "1dKkHnYnI633yuijQfTp2c",
            "@classname": "ProjectOccam::Node",
            "@uri": "http://192.168.99.10:8026/occam/api/v1/node/1dKkHnYnI633yuijQfTp2c"
        },
        {
            "@uuid": "1etJ2JLU0m0xJRYPD2GAyc",
            "@classname": "ProjectOccam::Node",
            "@uri": "http://192.168.99.10:8026/occam/api/v1/node/1etJ2JLU0m0xJRYPD2GAyc"
        },
        {
            "@uuid": "1fs9YAc647Tzxh1RrHACtq",
            "@classname": "ProjectOccam::Node",
            "@uri": "http://192.168.99.10:8026/occam/api/v1/node/1fs9YAc647Tzxh1RrHACtq"
        },
        {
            "@uuid": "1hWZE8LyvLJfxOoDP2tbKY",
            "@classname": "ProjectOccam::Node",
            "@uri": "http://192.168.99.10:8026/occam/api/v1/node/1hWZE8LyvLJfxOoDP2tbKY"
        },
        {
            "@uuid": "1iqntXADkLNDiMy6plT0as",
            "@classname": "ProjectOccam::Node",
            "@uri": "http://192.168.99.10:8026/occam/api/v1/node/1iqntXADkLNDiMy6plT0as"
        }
    ]
}
```

### Get a single object

You can get a single Object by adding the Object's UUID to the request path .

Example path:

    /occam/api/v1/node/123456789

_Returns the Node with the UUID of '123456789' if it exists._

### Get objects using filter

You can use GET variables to filter your requests. You may use exact property matching or regular expression filtering.

Example with exact: 

    /occam/api/v1/node?status=inactive

_matches any Node with status that equals 'inactive'_

Example using regex:

    /occam/api/v1/node?status=regex:(^active|^inactive)

_matches any Node with status that equals 'active' or 'inactive'_

Example using both:

    /occam/api/v1/node?status=regex:(^active|^inactive)&last_state=idle

_matches any Node with status that equals 'active' or 'inactive'_ and has a last_state of 'idle'.
 

## POST

### Creating an Object

To create an object we do a HTTP POST with the object values as a JSON Hash string for the parameter 'json_hash'. You will receive an error if you do not supply the required values depending on the Object you are trying to create.

The example below is for creating a Tag Rule and creating a Matcher for the Tag Rule. This is a two step process. Most other Objects just need one HTTP POST.

**Ruby**

```ruby
require "net/http"
require "json"

uri = URI "http://127.0.0.1:8026/occam/api/v1/tag"
rule_name = "example01" # Tag Rule name
tag = "example" # Tag Rule tag
json_hash = {"name" => rule_name, "tag" => tag} # Build our Hash
json_string = JSON.generate(json_hash) # Generate JSON String from Hash
# POST with our JSON String supplied as the value for 'json_hash"
res = Net::HTTP.post_form(uri, 'json_hash' => json_string) 
response_hash = JSON.parse(res.body)
unless res.class == Net::HTTPCreated # POST Response is HTTP Created (201) if successful
   raise "Error creating Tag Rule"
end

tag_rule_uuid = response_hash["response"].first["@uuid"]


# Now we add the Matcher to this Tag Rule
# Add the 'matcher' command on the end of the uri to access matcher actions
uri = URI "http://127.0.0.1:8026/occam/api/v1/tag/matcher"
json_hash = {"tag_rule_uuid" => tag_rule_uuid, # We supply the UUID from the Tag Rule above
             "key" =>     "virtual",
             "compare" => "equal",
             "value" =>   "vmware",
             "invert" =>  "false"
             }
json_string = JSON.generate(json_hash)
res = Net::HTTP.post_form(uri, 'json_hash' => json_string)
response_hash = JSON.parse(res.body)
unless res.class == Net::HTTPCreated
   raise "Error creating Matcher for Tag Rule #{tag_rule_uuid}"
end
```

**Perl**

```perl
use HTTP::Request::Common;
use LWP::UserAgent;
use JSON;

# Create our LWP::UserAgent     
$ua = new LWP::UserAgent;
# Create our JSON Hash
$json_hash = { name => "example01", # Tag Rule name
               tag  => "example" }; # Tag Rule tag
$json_string = to_json($json_hash); # Convert Hash to JSON String

# HTTP POST our JSON String as the value for 'json_hash'
$req = $ua->post('http://127.0.0.1:8026/occam/api/v1/tag',
                   [ json_hash => $json_string ]);
die "Error: ", $req->status_line
 unless $req->is_success;
$resp = $req->content; # Get the response content
$resp_hash = from_json($resp); # Parse JSON 
# Get the Tag Rule UUID for a newly created rule
$tag_rule_uuid = $resp_hash->{response}[0]->{'@uuid'};

# Now we add a Matcher to our new Tag Rule
# Create our Matcher Hash
$json_hash = { tag_rule_uuid => $tag_rule_uuid, # Tag Rule from above
               key      => "virtual",
               compare  => "equal",
               value    => "vmware",
               invert   => "false"};
$json_string = to_json($json_hash); # Convert Hash to JSON String

# HTTP POST our JSON String as the value for 'json_hash'
# We have added 'matcher' on the end of the URI to access matcher actions
$req = $ua->post('http://127.0.0.1:8026/occam/api/v1/tag/matcher',
                   [ json_hash => $json_string ]);
die "Error: ", $req->status_line
 unless $req->is_success;
```

**Python**

```python
import httplib
import urllib
import json

# Build or JSON Dictionary
json_dict = {'name': 'example01',
             'tag':  'example'}
# Convert JSOn Dictionary to JSON String
json_string = json.dumps(json_dict)
# Encode our JSON string as the value for 'json_hash'
params = urllib.urlencode({'json_hash': json_string})
# Set headers
headers = {"Content-type": "application/x-www-form-urlencoded",
            "Accept": "text/plain"}
conn = httplib.HTTPConnection('127.0.0.1:8026')
# HTTP POST to our URI
conn.request("POST", "/occam/api/v1/tag", params, headers)
resp = conn.getresponse()
# Check to be sure object was created
if resp.status != 201:
    raise Exception('Could not create Tag Rule')
    exit()
# Convert response back to Dictionary
json_resp = json.loads(resp.read())
# Get the UUID for our new Tag Rule
tag_rule_uuid = json_resp['response'][0]['@uuid']

# Create our JSON Dictionary for our Matcher
json_dict = {'tag_rule_uuid': tag_rule_uuid,
             'key':      'virtual',
             'compare':  'equal',
             'value':    'vmware',
             'invert':   'false'}
json_string = json.dumps(json_dict) # Convert to JSON String
params = urllib.urlencode({'json_hash': json_string})
conn = httplib.HTTPConnection('127.0.0.1:8026')
# We add 'matcher' to the URI path to access Matcher actions
conn.request("POST", "/occam/api/v1/tag/matcher", params, headers)
# HTTP POST to our URI adding the Matcher to the Tag Rule
resp = conn.getresponse() 
if resp.status != 201:
    raise Exception('Could not create Matcher')
    exit()
json_resp = json.loads(resp.read())
```

**C#**

This example uses [JSON.Net](http://james.newtonking.com/projects/json-net.aspx) for JSON Serialization/Deserialization.

```c#
static string CreateTagRule(string name, string tag)
{
    // Create our URL and HttpWebRequest
    string url = "http://192.168.99.10:8026/occam/api/v1/tag";
    HttpWebRequest req = WebRequest.Create(new Uri(url))
                            as HttpWebRequest;
    // Set header
    req.Method = "POST";
    req.ContentType = "application/x-www-form-urlencoded";
    // Create a Dictionary with our Matcher values
    Dictionary<string, string> dictionary = new Dictionary<string, string>();
    dictionary.Add("name", name);
    dictionary.Add("tag", tag); 
    // Convert Dictionary to JSON String
    String json_string = JsonConvert.SerializeObject(dictionary);
    // Create and encode our JSON String as 'json_hash'
    StringBuilder data = new StringBuilder();
    data.Append("json_hash=" + HttpUtility.UrlEncode(json_string));
    // Encode our parameters
    byte[] byteData = UTF8Encoding.UTF8.GetBytes(data.ToString());
    req.ContentLength = byteData.Length;  
 
    // Send the request:
    using (Stream post = req.GetRequestStream())
    {
        post.Write(byteData, 0, byteData.Length);
    }

    // Pick up the response:
    string result = null;
    using (HttpWebResponse resp = req.GetResponse()
                                    as HttpWebResponse)
    {
        StreamReader reader =
            new StreamReader(resp.GetResponseStream());
        result = reader.ReadToEnd();
    }
    // Get our Tag Rule UUID from the response
    Dictionary<string, object> json_result = JsonConvert.DeserializeObject<Dictionary<string, object>>(result);
    JContainer response_array = json_result["response"] as JContainer;
    Dictionary<string, object> tag_rule_dict = JsonConvert.DeserializeObject<Dictionary<string, object>>(response_array[0].ToString());
    String tag_rule_uuid = tag_rule_dict["@uuid"] as String;
    return tag_rule_uuid;
}

static string CreateMatcher(string tag_rule_uuid, string key, string compare, string value, string invert)
{
    // Create our URL and HttpWebRequest
    string url = "http://192.168.99.10:8026/occam/api/v1/tag/matcher";
    HttpWebRequest req = WebRequest.Create(new Uri(url))
                            as HttpWebRequest;
    // Set header
    req.Method = "POST";
    req.ContentType = "application/x-www-form-urlencoded";
    // Create a Dictionary with our Matcher values
    Dictionary<string, string> dictionary = new Dictionary<string, string>();
    dictionary.Add("tag_rule_uuid", tag_rule_uuid);
    dictionary.Add("key", key);
    dictionary.Add("compare", compare);
    dictionary.Add("value", value);
    dictionary.Add("invert", invert);
    // Convert Dictionary to JSON String
    String json_string = JsonConvert.SerializeObject(dictionary);
    // Create and encode our JSON String as 'json_hash'
    StringBuilder data = new StringBuilder();
    data.Append("json_hash=" + HttpUtility.UrlEncode(json_string));
    // Encode our parameters
    byte[] byteData = UTF8Encoding.UTF8.GetBytes(data.ToString());
    req.ContentLength = byteData.Length;

    // Send the request:
    using (Stream post = req.GetRequestStream())
    {
        post.Write(byteData, 0, byteData.Length);
    }

    // Pick up the response:
    string result = null;
    using (HttpWebResponse resp = req.GetResponse()
                                    as HttpWebResponse)
    {
        StreamReader reader =
            new StreamReader(resp.GetResponseStream());
        result = reader.ReadToEnd();
    }
    // Return entire response
    return result;
} 
```

**PowerShell**

This example is using the [PowerShell V3 RC](http://blogs.msdn.com/b/powershell/archive/2012/06/02/windows-management-framework-3-0-rc-is-available-for-download.aspx).

```powershell
# Create our URL
$url = "http://192.168.99.10:8026/occam/api/v1/tag"
# Create a Dictionary with our Tag Rule properties
$json_dict = @{"name" = "example01";
               "tag"  = "example"}
# Convert Dictionary to JSON String
$json_string = ConvertTo-Json $json_dict -Compress:$true
# Encode into param string
$param_string = "json_hash=" + [System.Web.HttpUtility]::UrlEncode($json_string)
# Build our web request
$webreq = [System.Net.WebRequest]::Create($url)
$webreq.Method = "POST"
$webreq.ContentType = "application/x-www-form-urlencoded"
$param = [System.Text.Encoding]::UTF8.GetBytes($param_string)
$webreq.ContentLength = $param.Length
# Make request
$req = $webreq.GetRequestStream()
$req.Write($param, 0, $param.length)
$req.Close()
# Get response
[System.Net.WebResponse] $resp = $webreq.GetResponse()
$rs = $resp.GetResponseStream()
[System.IO.StreamReader] $sr = New-Object System.IO.StreamReader -argumentList $rs
[string] $results = $sr.ReadToEnd()
# Get Tag Rule UUID from response
$json_results = ConvertFrom-Json $results
$tag_rule_uuid = $json_results.response."@uuid"
# Change URL to add 'matcher' command
$url = "http://192.168.99.10:8026/occam/api/v1/tag/matcher"
# Create new Dictionary with Tag Rule UUID
$json_dict = @{"tag_rule_uuid" = $tag_rule_uuid
               "key"     = "virtual";
               "compare" = "equal";
               "value"   = "vmware";
               "invert"  = "false";}
# Convert to JSON String
$json_string = ConvertTo-Json $json_dict -Compress:$true
# Encode into param string
$param_string = "json_hash=" + [System.Web.HttpUtility]::UrlEncode($json_string)
# Build our web request
$webreq = [System.Net.WebRequest]::Create($url)
$webreq.Method = "POST"
$webreq.ContentType = "application/x-www-form-urlencoded"
$param = [System.Text.Encoding]::UTF8.GetBytes($param_string)
$webreq.ContentLength = $param.Length
# Make request
$req = $webreq.GetRequestStream()
$req.Write($param, 0, $param.length)
$req.Close()
# Get response
[System.Net.WebResponse] $resp = $webreq.GetResponse()
$rs = $resp.GetResponseStream()
[System.IO.StreamReader] $sr = New-Object System.IO.StreamReader -argumentList $rs
[string] $results = $sr.ReadToEnd()
# Write out results
Write-Host $results
```

## PUT

### Updating an Object

To update objects that already exist you use the HTTP PUT method. You must have the UUID of the Object and refer to it by the appropriate Slice. You must also provide a key & value for the properties you wish to change. You must provide at least one value and some properties may not allow updating ('@template' for example).

Just like HTTP POST above, you PUT a JSON Hash as string with the variable name 'json_hash'. Unlike POST you have to append the UUID of the Object to the request. When getting an Object with HTTP GET the '@uri' property is the proper URI to use for updating.

The proper URI looks like:

    http://127.0.0.1:8026/occam/api/v1/policy/1234567890

_Where '1234567890' is the UUID of the Policy we wish to Update._

Our examples below are Updating an existing Policy.

**Ruby**

```ruby
require "net/http"
require "json"

# Our Policy UUID. Best practice is to get this from a GET request.
policy_uuid = "6ZCbx6TxEBN26yts9XKQp4"
json_hash = {"label" => "ESXi_Basic_Small", "tags" => "vmware,small_vm"}
json_string = JSON.generate(json_hash)
http = Net::HTTP.new('127.0.0.1','8026')
request = Net::HTTP::Put.new("/occam/api/v1/policy/#{policy_uuid}")
request.set_form_data({"json_hash" => json_string})
res = http.request(request)
response_hash = JSON.parse(res.body)
unless res.class == Net::HTTPAccepted
   raise "Error creating Tag Rule"
end
p response_hash
```

**Perl**

```perl
use HTTP::Request::Common;
use LWP::UserAgent;
use JSON;

# Create our LWP::UserAgent     
$ua = new LWP::UserAgent;
$url = 'http://127.0.0.1:8026/occam/api/v1/policy/6ZCbx6TxEBN26yts9XKQp4';
$json_hash = { label    => "ESXi_Small_Basic",
               tags      => "vmware,small_vm,perl",
               enabled  => "false"};
$json_string = to_json($json_hash); # Convert Hash to JSON String
$param = "json_hash=".$json_string;
# Make request
$request  = HTTP::Request::Common::PUT($url);
$request->header('Content-Type' => 'application/x-www-form-urlencoded');
$request->add_content_utf8( $param );
$request->header('Content-Length', length(${$request->content_ref}));
$req = $ua->request($request);

die "Error: ", $req->status_line
 unless $req->is_success;
```

**Python**

```python
import httplib
import urllib
import json

# Build or JSON Dictionary
json_dict = {'label':   'ESXi_Small_Basic',
             'tags':     'vmware,small_vm,python',
             'enabled': 'true'}
# Convert JSON Dictionary to JSON String
json_string = json.dumps(json_dict)
# Encode our JSON string as the value for 'json_hash'
params = urllib.urlencode({'json_hash': json_string})
# Set headers
headers = {"Content-type": "application/x-www-form-urlencoded",
            "Accept": "text/plain"}
conn = httplib.HTTPConnection('127.0.0.1:8026')
# HTTP POST to our URI
conn.request("PUT", "/occam/api/v1/policy/6ZCbx6TxEBN26yts9XKQp4", params, headers)
resp = conn.getresponse()
# Check to be sure object was updated
if resp.status != 202:
    raise Exception('Could not update Policy')
    exit()
```

**C#**

This example uses [JSON.Net](http://james.newtonking.com/projects/json-net.aspx) for JSON Serialization/Deserialization.

```c#
static string UpdatePolicy(string policy_uuid)
{
    string url = "http://192.168.99.10:8026/occam/api/v1/policy/" + policy_uuid;
    HttpWebRequest req = WebRequest.Create(new Uri(url))
                            as HttpWebRequest;
    req.Method = "PUT";
    req.ContentType = "application/x-www-form-urlencoded";

    Dictionary<string, string> dictionary = new Dictionary<string, string>();
    dictionary.Add("label",   "ESXi_Simple_Basic");
    dictionary.Add("tags",    "vmware,small_vm,csharp");
    dictionary.Add("enabled", "true");

    String json_string = JsonConvert.SerializeObject(dictionary);
    StringBuilder data = new StringBuilder();
    data.Append("json_hash=" + HttpUtility.UrlEncode(json_string));

    byte[] byteData = UTF8Encoding.UTF8.GetBytes(data.ToString());
    req.ContentLength = byteData.Length;

    // Send the request:
    using (Stream put = req.GetRequestStream())
    {
        put.Write(byteData, 0, byteData.Length);
    }

    // Pick up the response:
    string result = null;
    using (HttpWebResponse resp = req.GetResponse()
                                    as HttpWebResponse)
    {
        StreamReader reader =
            new StreamReader(resp.GetResponseStream());
        result = reader.ReadToEnd();
    }
    return result;
} 
```

**PowerShell**

This example is using the [PowerShell V3 RC](http://blogs.msdn.com/b/powershell/archive/2012/06/02/windows-management-framework-3-0-rc-is-available-for-download.aspx).

```powershell
# Create our URL
$url = "http://192.168.99.10:8026/occam/api/v1/policy/6ZCbx6TxEBN26yts9XKQp4"
# Create a Dictionary with our Policy properties we want to update
$json_dict = @{"label" = "ESXi_Small_Basic";
               "tags"  = "vmware,small_vm,powershell";
               "enabled" = "true"}
# Convert Dictionary to JSON String
$json_string = ConvertTo-Json $json_dict -Compress:$true
# Encode into param string
$param_string = "json_hash=" + [System.Web.HttpUtility]::UrlEncode($json_string)
# Build our web request
$webreq = [System.Net.WebRequest]::Create($url)
$webreq.Method = "PUT"
$webreq.ContentType = "application/x-www-form-urlencoded"
$param = [System.Text.Encoding]::UTF8.GetBytes($param_string)
$webreq.ContentLength = $param.Length
# Make request
$req = $webreq.GetRequestStream()
$req.Write($param, 0, $param.length)
$req.Close()
# Get response
[System.Net.WebResponse] $resp = $webreq.GetResponse()
$rs = $resp.GetResponseStream()
[System.IO.StreamReader] $sr = New-Object System.IO.StreamReader -argumentList $rs
[string] $results = $sr.ReadToEnd()
Write-Host $results
```

## DELETE

### Removing an Object

Removing an Object is extremely simple. You just need to do a HTTP DELETE to the URI for the Object. You do not need to pass any variables into the request.

You can get the Object's URI from a GET request. Some Slices enable using 'all' instead of the UUID on the path to remove all Objects.

Examples below are for deleting a Broker with the UUID '1234567890'

**Ruby**

```ruby
require "net/http"
require "json"

# Our Broker UUID. Best practice is to get this from a GET request.
broker_uuid = "1234567890"
http = Net::HTTP.new('127.0.0.1','8026')
request = Net::HTTP::Delete.new("/occam/api/v1/broker/#{broker_uuid}")
res = http.request(request)
response_hash = JSON.parse(res.body)
unless res.class == Net::HTTPAccepted
   raise "Error removing Broker"
end
p response_hash
```

**Perl**

```perl
use HTTP::Request::Common;
use LWP::UserAgent;
use JSON;

# Create our LWP::UserAgent     
$ua = new LWP::UserAgent;
$url = 'http://127.0.0.1:8026/occam/api/v1/broker/1234567890';
# Make request
$request  = HTTP::Request::Common::DELETE($url);
$req = $ua->request($request);

die "Error: ", $req->status_line
 unless $req->is_success;
```

**Python**

```python
import httplib
import urllib
import json

# Set headers
conn = httplib.HTTPConnection('127.0.0.1:8026')
# HTTP POST to our URI
conn.request("DELETE", "/occam/api/v1/broker/1234567890")
resp = conn.getresponse()
# Check to be sure object was created
if resp.status != 202:
    raise Exception('Could not remove Broker')
    exit()
```

**C#**

```c#
static string RemoveBroker(string broker_uuid)
{
    string url = "http://192.168.99.10:8026/occam/api/v1/broker/" + broker_uuid;
    HttpWebRequest req = WebRequest.Create(new Uri(url))
                            as HttpWebRequest;
    req.Method = "DELETE";
    string result = null;
    using (HttpWebResponse resp = req.GetResponse()
                                as HttpWebResponse)
    {
        StreamReader reader =
            new StreamReader(resp.GetResponseStream());
        result = reader.ReadToEnd();
    }
    return result;
} 
```

**PowerShell**

```powershell
$api_url = "http://192.168.99.10:8026/occam/api/v1/broker/6KyX4rIVBCTBleurWXbAFa"

$request = [System.Net.WebRequest]::Create($api_url)
$request.Method ="DELETE"
$request.ContentLength = 0

$response = $request.GetResponse()
$reader = new-object System.IO.StreamReader($response.GetResponseStream())
$reader.ReadToEnd()
```


# Error Handling

The Occam API responds with the appropriate HTTP Response Code depending on the request. Here are the successful response for each HTTP command:

1. GET = 200 OK
2. POST = 201 CREATED
3. PUT = 202 ACCEPTED
4. DELETE = 202 ACCEPTED

Error codes will depend on what failed about the request. Invalid requests will be different (400) than Forbidden (403) and Not Found (404). These can be used to capture if a Object is not there vs. an error with the format of a request.


# Note

This document is in flux as the Occam API is improved. Individual documentation per Slice will be coming soon.

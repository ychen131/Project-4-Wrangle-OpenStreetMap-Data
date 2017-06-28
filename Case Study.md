# OpenStreetMap Data Case Study

### Map Area
Central West London
- [http://www.openstreetmap.org/export#map=12/51.4935/-0.0783](http://www.openstreetmap.org/export#map=12/51.4935/-0.0783)
- [http://overpass-api.de/api/map?bbox=-0.2101,51.4596,-0.0807,51.5540](http://overpass-api.de/api/map?bbox=-0.2101,51.4596,-0.0807,51.5540) (This is the link to download the data set)

After living in the surburban for 4 years, my husband and I have decided to move to central west London. Hence I would like to this opportunity to explore the area on OpendStreetMap.



## Problems Encountered in the Map
After downloading the data set for central west London, I had a a quick scan through of the data and audit the street name and postcodes in the data set. The following problems were noted:

- Inconsistent case and typo in street names
- Over­abbreviated street names 
- Inconsistent abbreviation for "Saint"
- Inappropriate apostrophies within address
- Irrelevent postcodes
- Address with double postcodes

### Problems with street names
A list of expected street names, such as "Street" and "Road", are created. Regular expression is used to identify different types of street names. For any street names match the item within the lists are not investigated further.  

After ruing the "audit" function (see below), we noted there are a few issues with the street names.
```python
def audit(osmfile):
    osm_file = open(osmfile, "r")
    street_types = defaultdict(set)
    for event, elem in ET.iterparse(osm_file, events=("start",)):

        if elem.tag == "node" or elem.tag == "way":
            for tag in elem.iter("tag"):
                if is_street_name(tag):
                    audit_street_type(street_types, tag.attrib['v'])
```

#### Inconsistent case and typo in street name
Some street names are written in lower cases *("street")*. A few obvious typos also noted among street names, such as "Wqalk" and "Strreet".

#### Over abbreviation
A commen example for this paritular issue is showing "St" rather than "Street".

#### Inconsistent abbreviation for "Saint"
The word "Saint" is very common among street names within UK. It is usually displayed as an abbreviated version of "St". Two other versions also appeared within the data set, "Saint" and "St." *("Saint Alban's Grove" and "St Alban's Grove")*

In order to eliminate the above three issues, an "update_name" function is used:
```python
def update_name(name, mapping):
    name = string.capwords(name) 
    m = street_type_re.search(name)
    if m:
        street_type = m.group()
        if street_type not in expected and street_type in mapping:
            name = re.sub(street_type_re, mapping[street_type], name)
    else:
        print "Strange street name: " % name #print any strange street name does not match the regular expression

    if "Saint " in name or "St. " in name: 
        name = name.replace("Saint ", "St ")
        name = name.replace("St. ", "St ")
    return name
```

This updated all the street names so that they all start with a capital letter. Typos noted were fixed using a list of mapping. Different versions of "Saint" are all converted to "St" in the data set.

#### Inappropriate use of apostrophie"s
Apart from the issues noted above, it is also noted that some street names have inappropriate use of apostrophies. *(Princes Gardens", "Prince's Gardens" and "Princes's Gardens")*. Due to limited local knowledge and time comsuing nature of finding all the correct version of those street names containing apostrophies, these street names are left unfixed in the data set.


### Postcode Problems





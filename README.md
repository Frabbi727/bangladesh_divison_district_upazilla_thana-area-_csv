# 🇧🇩 Bangladesh Geo Data — Division, District, Upazila & Union

A **complete** and **community-friendly** dataset of Bangladesh's administrative geography including all **City Corporation Thanas** (Gulshan, Banani, Dhanmondi, Agrabad, Boalia etc.) that are missing from most other datasets.

Available in **CSV format** — ready to import into MySQL, PostgreSQL, SQLite, or any application.

---

## 📦 What's Inside

| File | Description | Rows |
|------|-------------|------|
| `divisions.csv` | All 8 divisions of Bangladesh | 8 |
| `districts.csv` | All 64 districts with lat/lon | 64 |
| `upazilas.csv` | All upazilas + city corporation thanas | 576 |
| `unions.csv` | All unions + city area wards | 4,855 |

---

## 🏙️ City Corporation Coverage (What Makes This Dataset Unique)

Most Bangladesh geo datasets only include rural upazilas and unions. **This dataset also includes city corporation thanas and their areas**, so you can find:

### Dhaka (DNCC + DSCC)
`Gulshan` · `Banani` · `Dhanmondi` · `Mirpur` · `Uttara East` · `Uttara West` · `Motijheel` · `Paltan` · `Mohammadpur` · `Tejgaon` · `Badda` · `Rampura` · `Jatrabari` · `Demra` · `Khilgaon` · `Shahbagh` · `Ramna` · `Wari` · `Lalbagh` · `Hazaribagh` · `Kafrul` · `Pallabi` · `Shah Ali` · `Cantonment` · and more...

### Chattogram City Corporation
`Double Mooring` · `Panchlaish` · `Pahartali` · `Bayazid Bostami` · `Bakalia` · `Patenga` · `Halishahar` · `Chandgaon` · `Akbar Shah` · `Kotwali`

### Other Cities
| City | Thanas Included |
|------|----------------|
| **Rajshahi** | Boalia, Rajpara, Shah Makhdum, Motihar |
| **Khulna** | Sonadanga, Khalishpur, Khan Jahan Ali, Daulatpur, Labanchara, Horintana |
| **Sylhet** | Sylhet Sadar, Kotwali, South Surma, Shahporan, Airport |
| **Barisal** | Kotwali, Barisal Sadar, Airport Area |
| **Gazipur** | Gazipur Sadar, Tongi East, Tongi West, Kaliakoir |
| **Narayanganj** | Narayanganj Sadar, Siddhirganj, Fatullah, Bandor |
| **Mymensingh** | Mymensingh Sadar City, Kotwali |
| **Comilla** | Comilla Sadar City, Kotwali |
| **Rangpur** | Rangpur Sadar City, Kotwali |

---

## 🗂️ Data Structure

### `divisions.csv`
```
id, name, bn_name, url
```
```csv
"6","Dhaka","ঢাকা","www.dhakadiv.gov.bd"
```

### `districts.csv`
```
id, division_id, name, bn_name, lat, lon, url
```
```csv
"47","6","Dhaka","ঢাকা","23.7115253","90.4111451","www.dhaka.gov.bd"
```

### `upazilas.csv`
```
id, district_id, name, bn_name, url
```
```csv
"495","47","Gulshan","গুলশান","null"
"496","47","Banani","বনানী","null"
"516","47","Dhanmondi","ধানমন্ডি","null"
```

### `unions.csv`
```
id, upazila_id, name, bn_name, url
```
```csv
"4541","495","Gulshan-1","গুলশান-১","null"
"4542","495","Gulshan-2","গুলশান-২","null"
"4543","495","Baridhara","বারিধারা","null"
```

---

## 🚀 How to Use

### MySQL / MariaDB

Create your tables first:

```sql
CREATE TABLE divisions (
  id INT PRIMARY KEY,
  name VARCHAR(50),
  bn_name VARCHAR(50),
  url VARCHAR(100)
);

CREATE TABLE districts (
  id INT PRIMARY KEY,
  division_id INT,
  name VARCHAR(50),
  bn_name VARCHAR(50),
  lat VARCHAR(20),
  lon VARCHAR(20),
  url VARCHAR(100),
  FOREIGN KEY (division_id) REFERENCES divisions(id)
);

CREATE TABLE upazilas (
  id INT PRIMARY KEY,
  district_id INT,
  name VARCHAR(100),
  bn_name VARCHAR(100),
  url VARCHAR(100),
  FOREIGN KEY (district_id) REFERENCES districts(id)
);

CREATE TABLE unions (
  id INT PRIMARY KEY,
  upazila_id INT,
  name VARCHAR(100),
  bn_name VARCHAR(100),
  url VARCHAR(100),
  FOREIGN KEY (upazila_id) REFERENCES upazilas(id)
);
```

Then import in this order:

```sql
LOAD DATA INFILE '/path/to/divisions.csv'
INTO TABLE divisions
FIELDS TERMINATED BY ',' ENCLOSED BY '"'
LINES TERMINATED BY '\n';

LOAD DATA INFILE '/path/to/districts.csv'
INTO TABLE districts
FIELDS TERMINATED BY ',' ENCLOSED BY '"'
LINES TERMINATED BY '\n';

LOAD DATA INFILE '/path/to/upazilas.csv'
INTO TABLE upazilas
FIELDS TERMINATED BY ',' ENCLOSED BY '"'
LINES TERMINATED BY '\n';

LOAD DATA INFILE '/path/to/unions.csv'
INTO TABLE unions
FIELDS TERMINATED BY ',' ENCLOSED BY '"'
LINES TERMINATED BY '\n';
```

> ⚠️ Always import in the order: **divisions → districts → upazilas → unions** to respect foreign key constraints.

---

### PostgreSQL

```sql
COPY divisions FROM '/path/to/divisions.csv' DELIMITER ',' CSV QUOTE '"';
COPY districts FROM '/path/to/districts.csv' DELIMITER ',' CSV QUOTE '"';
COPY upazilas FROM '/path/to/upazilas.csv' DELIMITER ',' CSV QUOTE '"';
COPY unions FROM '/path/to/unions.csv' DELIMITER ',' CSV QUOTE '"';
```

---

### PHP (Laravel Example)

```php
// Seed divisions
$divisions = array_map('str_getcsv', file('divisions.csv'));
foreach ($divisions as $row) {
    DB::table('divisions')->insert([
        'id'      => $row[0],
        'name'    => $row[1],
        'bn_name' => $row[2],
        'url'     => $row[3],
    ]);
}
```

---

### JavaScript / Node.js

```js
const fs = require('fs');
const csv = require('csv-parser');

fs.createReadStream('upazilas.csv')
  .pipe(csv(['id', 'district_id', 'name', 'bn_name', 'url']))
  .on('data', (row) => {
    console.log(row.name); // Gulshan, Banani, Dhanmondi...
  });
```

---

### Python

```python
import pandas as pd

divisions = pd.read_csv('divisions.csv', header=None,
                        names=['id','name','bn_name','url'])
districts = pd.read_csv('districts.csv', header=None,
                        names=['id','division_id','name','bn_name','lat','lon','url'])
upazilas  = pd.read_csv('upazilas.csv', header=None,
                        names=['id','district_id','name','bn_name','url'])
unions    = pd.read_csv('unions.csv', header=None,
                        names=['id','upazila_id','name','bn_name','url'])

# Get all Dhaka city thanas
dhaka_thanas = upazilas[upazilas['district_id'] == 47]
print(dhaka_thanas['name'].tolist())
# ['Savar', 'Dhamrai', ..., 'Gulshan', 'Banani', 'Dhanmondi', ...]
```

---

## ❓ Why Gulshan / Banani / Dhanmondi Were Missing Before

Standard Bangladesh administrative data follows the rural hierarchy:

```
Division → District → Upazila → Union
```

City corporation areas like Gulshan, Banani, Dhanmondi etc. are **thanas under City Corporations** (DNCC / DSCC), a completely separate administrative structure not included in most datasets. This repo fills that gap by adding city corporation thanas as upazilas and their wards/areas as unions under the correct district.

---

## 🤝 Contributing

Missing an area? Found a spelling mistake? Want to add postcodes or GPS coordinates?

1. **Fork** this repository
2. Make your changes to the relevant CSV file
3. Open a **Pull Request** with a clear description of what you added or fixed
4. Please keep the same CSV format: `id, parent_id, name, bn_name, url`

### Things You Can Help With
- [ ] Add missing village/para level data
- [ ] Add GPS coordinates for city thanas
- [ ] Add postcode/zip code column
- [ ] Add Gazipur, Narayanganj ward-level detail
- [ ] Fix any Bengali name spelling errors
- [ ] Add JSON / SQL versions of the data

---

## 📋 Data Sources

- [bangladesh.gov.bd](https://www.bangladesh.gov.bd) — Official government portal
- [Bangladesh Bureau of Statistics (BBS)](http://www.bbs.gov.bd)
- [Dhaka North City Corporation (DNCC)](https://www.dncc.gov.bd)
- [Dhaka South City Corporation (DSCC)](https://www.dscc.gov.bd)
- Wikipedia — Thana and ward boundary references
- Community contributions

---

## 📄 License

This dataset is released under the **MIT License** — free to use in personal and commercial projects. Attribution appreciated but not required.

---

## ⭐ Support

If this dataset helped your project, please consider giving it a **star** on GitHub — it helps others find it!

Have questions or suggestions? Open an **Issue** and we'll get back to you.

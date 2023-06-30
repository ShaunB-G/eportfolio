<a href="https://colab.research.google.com/github/ShaunB-G/eportfolio/blob/main/AssessmentDB.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>


```python
!pip install mysql.connector

```


```python
import mysql.connector

mydb = mysql.connector.connect(
  host="sql8.freesqldatabase.com",
  user="sql8629511",
  password="piCnnyCvCH"
)
mycursor = mydb.cursor()


```


```python
##Create db /already available
#mycursor.execute("CREATE DATABASE DreamlandManor")

#Check db was created
mycursor.execute("SHOW DATABASES")

for x in mycursor:
  print(x)
```

    ('information_schema',)
    ('sql8629511',)
    


```python
##Connect to db
mydb = mysql.connector.connect(
  host="sql8.freesqldatabase.com",
  user="sql8629511",
  password="piCnnyCvCH",
  database="sql8629511"
)

mycursor = mydb.cursor()
```


```python
##Create tables
mycursor.execute("CREATE TABLE Hotel (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(255), description varchar(255), rating varchar(255), email varchar(255),country varchar(255), city varchar(255), street varchar(255), phone varchar(255))")
mycursor.execute("CREATE TABLE Rooms (id INT AUTO_INCREMENT PRIMARY KEY, hotelId integer, shortname varchar(255), status varchar(255), price float, type varchar(255), capacity integer, desription varchar(255))")
mycursor.execute("CREATE TABLE Amenities (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(255))")
mycursor.execute("CREATE TABLE Users (id INT AUTO_INCREMENT PRIMARY KEY, firstName varchar(255), lastName varchar(255))")
mycursor.execute("CREATE TABLE Gallery (id INT AUTO_INCREMENT PRIMARY KEY, roomId integer, type varchar(255), url varchar(255))")
mycursor.execute("CREATE TABLE RoomsAmentities (roomid INT AUTO_INCREMENT PRIMARY KEY, amenityID integer)")
mycursor.execute("CREATE TABLE Reservations (id INT AUTO_INCREMENT PRIMARY KEY, roomId integer, userId integer, checkin date, checkout date, status varchar(255), notes varchar(255), FOREIGN KEY (roomId) REFERENCES Rooms (id))")
mycursor.execute("CREATE TABLE UsersIdentities (userId INT AUTO_INCREMENT PRIMARY KEY, identityId integer, FOREIGN KEY (userId) REFERENCES Users (id))")
mycursor.execute("CREATE TABLE Billing (id INT AUTO_INCREMENT PRIMARY KEY, reservationId integer, userId integer, billNumber varchar(255), amount float, status varchar(255), billed date, paid date, paymentReference varchar(255), receiptNumber varchar(255))")
mycursor.execute("CREATE TABLE Identities (id INT AUTO_INCREMENT PRIMARY KEY, hotelId integer, email varchar(255), password varchar(255), role varchar(255))")

#Check tables were created
mycursor.execute("SHOW TABLES")
for x in mycursor:
  print(x)
```

    ('Amenities',)
    ('Billing',)
    ('Gallery',)
    ('Hotel',)
    ('Identities',)
    ('Reservations',)
    ('Rooms',)
    ('RoomsAmentities',)
    ('Users',)
    ('UsersIdentities',)
    


```python
mycursor.execute("ALTER TABLE Rooms ADD FOREIGN KEY (hotelId) REFERENCES Hotel (id)")

mycursor.execute("ALTER TABLE Identities ADD FOREIGN KEY (hotelId) REFERENCES Hotel (id)")

mycursor.execute("ALTER TABLE UsersIdentities ADD FOREIGN KEY (userId) REFERENCES Users (id)")

mycursor.execute("ALTER TABLE UsersIdentities ADD FOREIGN KEY (identityId) REFERENCES Identities (id)")

mycursor.execute("ALTER TABLE RoomsAmentities ADD FOREIGN KEY (roomId) REFERENCES Rooms (id)")

mycursor.execute("ALTER TABLE RoomsAmentities ADD FOREIGN KEY (amenityID) REFERENCES Amenities (id)")

mycursor.execute("ALTER TABLE Reservations ADD FOREIGN KEY (roomId) REFERENCES Rooms (id)")

mycursor.execute("ALTER TABLE Reservations ADD FOREIGN KEY (userId) REFERENCES Users (id)")

mycursor.execute("ALTER TABLE Billing ADD FOREIGN KEY (reservationId) REFERENCES Reservations (id)")

mycursor.execute("ALTER TABLE Gallery ADD FOREIGN KEY (roomId) REFERENCES Rooms (id)")

```


```python

```


```python
#Populating tables with data
#install the faker package
!pip install Faker

# Import the necessary packages
from faker import Faker
import datetime

# Create an instance of the Faker generator
fake = Faker()
```

    Requirement already satisfied: Faker in /usr/local/lib/python3.10/dist-packages (18.11.2)
    Requirement already satisfied: python-dateutil>=2.4 in /usr/local/lib/python3.10/dist-packages (from Faker) (2.8.2)
    Requirement already satisfied: six>=1.5 in /usr/local/lib/python3.10/dist-packages (from python-dateutil>=2.4->Faker) (1.16.0)
    


```python
# Truncate existing tables
mycursor.execute("SET FOREIGN_KEY_CHECKS = 0")
mycursor.execute("TRUNCATE TABLE Hotel")
mycursor.execute("TRUNCATE TABLE Rooms")
mycursor.execute("TRUNCATE TABLE Amenities")
mycursor.execute("TRUNCATE TABLE Users")
mycursor.execute("TRUNCATE TABLE Gallery")
mycursor.execute("TRUNCATE TABLE RoomsAmentities")
mycursor.execute("TRUNCATE TABLE Reservations")
mycursor.execute("TRUNCATE TABLE UsersIdentities")
mycursor.execute("TRUNCATE TABLE Billing")
mycursor.execute("TRUNCATE TABLE Identities")
mycursor.execute("SET FOREIGN_KEY_CHECKS = 1")
```


```python
hotel_name = "Dreamland Manor"
hotel_description = fake.sentence()
hotel_rating = fake.random_int(min=1, max=5)
hotel_email = fake.email()
hotel_country = fake.country()
hotel_city = fake.city()
hotel_street = fake.street_address()
hotel_phone = fake.phone_number()
mycursor.execute(f"INSERT INTO Hotel (name, description, rating, email, country, city, street, phone) VALUES ('{hotel_name}', '{hotel_description}', '{hotel_rating}', '{hotel_email}', '{hotel_country}', '{hotel_city}', '{hotel_street}', '{hotel_phone}')")


mycursor.execute("SELECT * FROM Hotel")

hotel = mycursor.fetchall()
hotel_id = hotel[0][0]
print(hotel_id)
for x in hotel:
  print(x)
```

    1
    (1, 'Dreamland Manor', 'While camera mission many policy edge.', '5', 'ttyler@example.com', 'Hong Kong', 'South Jamesbury', '0957 Roman Plain Suite 064', '(306)435-5567x7915')
    


```python

# Create and execute insert statements to populate the tables with 1000 records
for _ in range(10):
    # Insert statement for Rooms table
    room_hotel_id = hotel_id #forign key to hotel
    room_shortname = fake.random_letter() + fake.random_letter()
    room_status = fake.random_element(elements=('Available', 'Occupied', 'Maintenance'))
    room_price = fake.random_int(min=50, max=200)
    room_type = fake.random_element(elements=('Standard', 'Deluxe', 'Suite'))
    room_capacity = fake.random_int(min=1, max=4)
    room_description = fake.sentence()
    roomcursor = mydb.cursor()
    roomcursor.execute(f"INSERT INTO Rooms (hotelId, shortname, status, price, type, capacity, desription) VALUES ('{room_hotel_id}', '{room_shortname}', '{room_status}', '{room_price}', '{room_type}', '{room_capacity}', '{room_description}')")

    # Insert statement for Gallery table
    gallery_room_id = roomcursor.lastrowid
    gallery_type = fake.random_element(elements=('Image', 'Video'))
    gallery_url = fake.url()
    mycursor.execute(f"INSERT INTO Gallery (roomId, type, url) VALUES ('{gallery_room_id}', '{gallery_type}', '{gallery_url}')")

    # Insert statement for Amenities table
    amenity_name = fake.word()
    amenitycursor = mydb.cursor()
    amenitycursor.execute(f"INSERT INTO Amenities (name) VALUES ('{amenity_name}')")

    # Insert statement for RoomsAmentities table
    mycursor.execute(f"INSERT INTO RoomsAmentities (roomid, amenityID) VALUES ('{roomcursor.lastrowid}', '{amenitycursor.lastrowid}')")

    # Insert statement for Users table
    user_first_name = fake.first_name()
    user_last_name = fake.last_name()
    usercursor = mydb.cursor()
    usercursor.execute(f"INSERT INTO Users (firstName, lastName) VALUES ('{user_first_name}', '{user_last_name}')")



    # Insert statement for Reservations table
    reservation_room_id = roomcursor.lastrowid
    reservation_user_id = usercursor.lastrowid
    reservation_checkin = fake.date_between(start_date='-1y', end_date='+1y')
    reservation_checkout = fake.date_between_dates(date_start=reservation_checkin, date_end=reservation_checkin+datetime.timedelta(days=5))
    reservation_status = fake.random_element(elements=('Confirmed', 'Pending', 'Cancelled'))
    reservation_notes = fake.sentence()
    reservationcursor = mydb.cursor()
    reservationcursor.execute(f"INSERT INTO Reservations (roomId, userId, checkin, checkout, status, notes) VALUES ('{reservation_room_id}', '{reservation_user_id}', '{reservation_checkin}', '{reservation_checkout}', '{reservation_status}', '{reservation_notes}')")

    # Insert statement for UsersIdentities table
    #user_identity_id = fake.random_int(min=1, max=100)
    #mycursor.execute(f"INSERT INTO UsersIdentities (userId, identityId) VALUES ('{reservation_user_id}', '{user_identity_id}')")

    # Insert statement for Billing table
    billing_reservation_id = reservationcursor.lastrowid
    billing_user_id = usercursor.lastrowid
    billing_number = fake.random_number(digits=6)
    billing_amount = fake.random_int(min=100, max=500)
    billing_status = fake.random_element(elements=('Paid', 'Unpaid'))
    billing_billed = fake.date_between(start_date='-1y', end_date='today')
    billing_paid = fake.date_between_dates(date_start=billing_billed, date_end=billing_billed+datetime.timedelta(days=10))
    billing_reference = fake.random_number(digits=6)
    billing_receipt = fake.random_number(digits=6)
    mycursor.execute(f"INSERT INTO Billing (reservationId, userId, billNumber, amount, status, billed, paid, paymentReference, receiptNumber) VALUES ('{billing_reservation_id}', '{billing_user_id}', '{billing_number}', '{billing_amount}', '{billing_status}', '{billing_billed}', '{billing_paid}', '{billing_reference}', '{billing_receipt}')")

    # Insert statement for Identities table
    identity_hotel_id = hotel_id
    identity_email = fake.email()
    identity_password = fake.password()
    identity_role = fake.random_element(elements=('Admin', 'Employee','Guest'))
    mycursor.execute(f"INSERT INTO Identities (hotelId, email, password, role) VALUES ('{identity_hotel_id}', '{identity_email}', '{identity_password}', '{identity_role}')")

# Commit the changes to the database
mydb.commit()
```


```python
#check if all table
# Check and count records in each table

# Hotel table
mycursor.execute("SELECT COUNT(*) FROM Hotel")
hotel_count = mycursor.fetchone()[0]
print("Hotel Count:", hotel_count)

# Rooms table
mycursor.execute("SELECT COUNT(*) FROM Rooms")
rooms_count = mycursor.fetchone()[0]
print("Rooms Count:", rooms_count)

# Amenities table
mycursor.execute("SELECT COUNT(*) FROM Amenities")
amenities_count = mycursor.fetchone()[0]
print("Amenities Count:", amenities_count)

# Users table
mycursor.execute("SELECT COUNT(*) FROM Users")
users_count = mycursor.fetchone()[0]
print("Users Count:", users_count)

# Gallery table
mycursor.execute("SELECT COUNT(*) FROM Gallery")
gallery_count = mycursor.fetchone()[0]
print("Gallery Count:", gallery_count)

# RoomsAmentities table
mycursor.execute("SELECT COUNT(*) FROM RoomsAmentities")
rooms_amenities_count = mycursor.fetchone()[0]
print("RoomsAmenities Count:", rooms_amenities_count)

# Reservations table
mycursor.execute("SELECT COUNT(*) FROM Reservations")
reservations_count = mycursor.fetchone()[0]
print("Reservations Count:", reservations_count)

# UsersIdentities table
mycursor.execute("SELECT COUNT(*) FROM UsersIdentities")
users_identities_count = mycursor.fetchone()[0]
print("UsersIdentities Count:", users_identities_count)

# Billing table
mycursor.execute("SELECT COUNT(*) FROM Billing")
billing_count = mycursor.fetchone()[0]
print("Billing Count:", billing_count)

# Identities table
mycursor.execute("SELECT COUNT(*) FROM Identities")
identities_count = mycursor.fetchone()[0]
print("Identities Count:", identities_count)

```

    Hotel Count: 1
    Rooms Count: 11
    Amenities Count: 11
    Users Count: 11
    Gallery Count: 11
    RoomsAmenities Count: 11
    Reservations Count: 10
    UsersIdentities Count: 0
    Billing Count: 10
    Identities Count: 10
    


```python
# Fetch records from each table
# Hotel table
mycursor.execute("SELECT * FROM Hotel")
hotel_records = mycursor.fetchall()
print("Hotel Records:")
for record in hotel_records:
    print(record)

# Rooms table
mycursor.execute("SELECT * FROM Rooms")
rooms_records = mycursor.fetchall()
print("Rooms Records:")
for record in rooms_records:
    print(record)

# Amenities table
mycursor.execute("SELECT * FROM Amenities")
amenities_records = mycursor.fetchall()
print("Amenities Records:")
for record in amenities_records:
    print(record)

# Users table
mycursor.execute("SELECT * FROM Users")
users_records = mycursor.fetchall()
print("Users Records:")
for record in users_records:
    print(record)

# Gallery table
mycursor.execute("SELECT * FROM Gallery")
gallery_records = mycursor.fetchall()
print("Gallery Records:")
for record in gallery_records:
    print(record)


# Reservations table
mycursor.execute("SELECT * FROM Reservations")
reservations_records = mycursor.fetchall()
print("Reservations Records:")
for record in reservations_records:
    print(record)

# UsersIdentities table
mycursor.execute("SELECT * FROM UsersIdentities")
users_identities_records = mycursor.fetchall()
print("UsersIdentities Records:")
for record in users_identities_records:
    print(record)

# Billing table
mycursor.execute("SELECT * FROM Billing")
billing_records = mycursor.fetchall()
print("Billing Records:")
for record in billing_records:
    print(record)

# Identities table
mycursor.execute("SELECT * FROM Identities")
identities_records = mycursor.fetchall()
print("Identities Records:")
for record in identities_records:
    print(record)

```

    Hotel Records:
    (1, 'Dreamland Manor', 'While camera mission many policy edge.', '5', 'ttyler@example.com', 'Hong Kong', 'South Jamesbury', '0957 Roman Plain Suite 064', '(306)435-5567x7915')
    Rooms Records:
    (1, 1, 'VT', 'Occupied', 164.0, 'Suite', 1, 'Cut accept do support American soon behind.')
    (2, 1, 'FN', 'Available', 181.0, 'Deluxe', 2, 'Class several whom.')
    (3, 1, 'xU', 'Available', 62.0, 'Suite', 1, 'Write amount wait wind news if either.')
    (4, 1, 'vg', 'Occupied', 137.0, 'Suite', 3, 'Move join system culture run money true.')
    (5, 1, 'RO', 'Available', 139.0, 'Deluxe', 1, 'Place range arrive Democrat sit out.')
    (6, 1, 'LO', 'Occupied', 101.0, 'Deluxe', 2, 'Among scene base Democrat author shoulder hard thank.')
    (7, 1, 'kA', 'Occupied', 172.0, 'Standard', 1, 'Eat policy cover people.')
    (8, 1, 'QL', 'Maintenance', 95.0, 'Suite', 3, 'Statement team edge foot.')
    (9, 1, 'Ll', 'Maintenance', 70.0, 'Deluxe', 1, 'Since identify name inside bad special.')
    (10, 1, 'uW', 'Occupied', 193.0, 'Suite', 4, 'Such ability out particular.')
    (11, 1, 'XN', 'Available', 149.0, 'Standard', 2, 'Heavy present eye line.')
    Amenities Records:
    (1, 'beyond')
    (2, 'identify')
    (3, 'reduce')
    (4, 'news')
    (5, 'space')
    (6, 'compare')
    (7, 'write')
    (8, 'his')
    (9, 'many')
    (10, 'new')
    (11, 'time')
    Users Records:
    (1, 'James', 'Wright')
    (2, 'Morgan', 'Webb')
    (3, 'Deanna', 'Hinton')
    (4, 'Lisa', 'Thompson')
    (5, 'Cody', 'Daniels')
    (6, 'Corey', 'Jimenez')
    (7, 'Ryan', 'Mcknight')
    (8, 'Jose', 'Hall')
    (9, 'Andrea', 'Garcia')
    (10, 'Melissa', 'Miles')
    (11, 'Andrew', 'Leonard')
    Gallery Records:
    (1, 1, 'Video', 'https://www.erickson-shaw.com/')
    (2, 2, 'Video', 'https://www.vang.com/')
    (3, 3, 'Image', 'https://www.simpson.org/')
    (4, 4, 'Image', 'https://www.evans.biz/')
    (5, 5, 'Video', 'http://davis.com/')
    (6, 6, 'Video', 'https://www.rodgers.com/')
    (7, 7, 'Video', 'http://www.mann-day.com/')
    (8, 8, 'Video', 'http://www.schultz-clark.info/')
    (9, 9, 'Video', 'https://martinez-gonzales.com/')
    (10, 10, 'Video', 'http://woods.org/')
    (11, 11, 'Image', 'http://stein-hood.com/')
    Reservations Records:
    (1, 2, 2, datetime.date(2023, 1, 27), datetime.date(2023, 1, 27), 'Cancelled', 'Quality here especially wife step study.')
    (2, 3, 3, datetime.date(2024, 3, 29), datetime.date(2024, 3, 31), 'Cancelled', 'Worry onto cost arm month general spring.')
    (3, 4, 4, datetime.date(2023, 2, 7), datetime.date(2023, 2, 7), 'Cancelled', 'Land marriage begin budget.')
    (4, 5, 5, datetime.date(2022, 8, 9), datetime.date(2022, 8, 10), 'Confirmed', 'Operation happy agent table rise your unit mission.')
    (5, 6, 6, datetime.date(2023, 4, 7), datetime.date(2023, 4, 7), 'Pending', 'Spring impact address out again.')
    (6, 7, 7, datetime.date(2023, 8, 10), datetime.date(2023, 8, 11), 'Cancelled', 'International west rich sort will impact.')
    (7, 8, 8, datetime.date(2024, 6, 2), datetime.date(2024, 6, 3), 'Pending', 'Than draw line anything Congress it yet.')
    (8, 9, 9, datetime.date(2023, 4, 3), datetime.date(2023, 4, 4), 'Confirmed', 'Who recently field large ten moment kid join.')
    (9, 10, 10, datetime.date(2023, 3, 31), datetime.date(2023, 4, 3), 'Pending', 'Blood party cost section build listen material.')
    (10, 11, 11, datetime.date(2023, 6, 10), datetime.date(2023, 6, 13), 'Cancelled', 'Network work environmental speak.')
    UsersIdentities Records:
    Billing Records:
    (1, 1, 2, '204096', 486.0, 'Paid', datetime.date(2023, 3, 19), datetime.date(2023, 3, 20), '477646', '560271')
    (2, 2, 3, '414703', 115.0, 'Paid', datetime.date(2023, 1, 2), datetime.date(2023, 1, 4), '959471', '775298')
    (3, 3, 4, '642788', 246.0, 'Paid', datetime.date(2022, 10, 8), datetime.date(2022, 10, 17), '490091', '761563')
    (4, 4, 5, '584681', 309.0, 'Paid', datetime.date(2023, 6, 27), datetime.date(2023, 6, 27), '971207', '552490')
    (5, 5, 6, '723821', 167.0, 'Unpaid', datetime.date(2023, 3, 27), datetime.date(2023, 3, 31), '955113', '24711')
    (6, 6, 7, '133067', 421.0, 'Unpaid', datetime.date(2022, 9, 14), datetime.date(2022, 9, 23), '962603', '80857')
    (7, 7, 8, '348639', 475.0, 'Unpaid', datetime.date(2023, 6, 27), datetime.date(2023, 6, 28), '317172', '590712')
    (8, 8, 9, '808720', 472.0, 'Unpaid', datetime.date(2022, 12, 3), datetime.date(2022, 12, 5), '347247', '527735')
    (9, 9, 10, '618873', 427.0, 'Paid', datetime.date(2022, 10, 3), datetime.date(2022, 10, 8), '931881', '373277')
    (10, 10, 11, '748531', 495.0, 'Unpaid', datetime.date(2023, 2, 3), datetime.date(2023, 2, 11), '469751', '314813')
    Identities Records:
    (1, 1, 'julie87@example.com', '(zl2ZEfwoR', 'Admin')
    (2, 1, 'bridgeswilliam@example.com', 'bR0H%rQu&#', 'Admin')
    (3, 1, 'robertsherman@example.org', 'M*2ARF3b^Y', 'Admin')
    (4, 1, 'adamschad@example.net', '$7MPbHox+&', 'Employee')
    (5, 1, 'rogerskurt@example.org', '^jJ61EvhJ0', 'Admin')
    (6, 1, 'hmartin@example.net', '*Pl8ZYku&b', 'Admin')
    (7, 1, 'ylarsen@example.net', '!b0v*5XiKM', 'Guest')
    (8, 1, 'clindsey@example.net', '^C0VBHxQ39', 'Employee')
    (9, 1, 'david76@example.com', 'tu8TxUxQ_Y', 'Admin')
    (10, 1, 'kayla29@example.org', '%FTAwc*O27', 'Employee')
    

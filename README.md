# Data Capture and Analysis of Cab rides
### Problem Statement:
This project is in related to a mobility start-up company called 'Your Own Cabs' (YOC). Primarily, it is an online on-demand cab booking service. Owing to a successful business model and excellent service, the company’s business is growing rapidly, and these numbers are breaking their own records every day. YOC’s customer base and ride counts are increasing on a day-by-day basis. 

It is highly important for business stakeholders to derive quick and on-demand insights regarding the numbers to decide the company’s future strategy. Owing to the massive growth in business, it is getting tough for the company’s management to obtain the business numbers frequently, as backend MySQL is not capable of catering to all types of queries due to the large volume of data. The following statistics will help you understand the gravity of the situation:
![image](https://github.com/Mohammed-Zeeshaan-Saqeeb/Data_Capture_and_Analysis_of_Cab_rides/assets/77388869/115909cc-09fd-437e-a0e5-e8cb7473d901)

![image](https://github.com/Mohammed-Zeeshaan-Saqeeb/Data_Capture_and_Analysis_of_Cab_rides/assets/77388869/7d12c676-4bd7-4143-b92b-3a764a7936b3)

With a rising number of users and rides, the company’s major focus is to provide its customers with a delightful experience while ensuring zero downtime. Also, business stakeholders should be provided with all the answers that they require that are backed by data. Data-driven decisions play a steroid role for a fast-growing start-up.

As a big data engineer, I have to  architect and build a solution to cater to the following requirements:

 

**Booking data analytics solution**: This is a feature in which the ride-booking data is stored in a way such that it is available for all types of analytics without hampering business. These analyses mostly include daily, weekly and monthly booking counts as well as booking counts by the mobile operating system, average booking amount, total tip amount, etc.

**Clickstream data analytics solution:** The clickstream is the application browsing data that is generated for every action taken by the user on the app. This includes link click, button click, screen load, etc. This is relevant to knowing the user’s intent and then taking action to engage them more in the app according to their browsing behavior. Since this is very high-volume data (more than the bookings data), it needs to be architected quite well and stored in a proper format for analysis.

### Data
The following data will be used for this problem:

**bookings** (The booking data is added to/updated in this table after a booking/ride is successfully completed.) 

booking_id: Booking ID String

customer_id: Customer ID Number

driver_id: Driver ID Number

customer_app_version: Customer App Version String

customer_phone_os_version: Customer mobile operating system

pickup_lat: Pickup latitude

pickup_lon: Pickup longitude

drop_lat: Dropoff latitude

drop_lon: Dropoff longitude

pickup_timestamp: Timestamp at which customer was picked up

drop_timestamp: Timestamp at which customer was dropped at destination

trip_fare: Total amount of the trip

tip_amount: Tip amount given by customer to driver for this ride

currency_code: Currency Code String for the amount paid by customer

cab_color: Color of the cab

cab_registration_no: Registration number string of the vehicle

customer_rating_by_driver: Rating number given by driver to customer after ride

rating_by_customer: Rating number given by customer to driver after ride

passenger_count: Total count of passengers who boarded the cab for ride

Since booking data is updated by the backend team in MySQL, it is stored in a central AWS RDS. We were  given the already loaded booking table data.

**clickstream** (All user’s activity data such as click and page load):

customer_id: Customer ID Number

app_version: Customer App Version String

os_version: User mobile operating system

lat: Latitude

lon: Longitude

page_id: UUID of the page/screen browsed by a user

button_id: UUID of the button clicked by a user

is_button_click: Yes/No depending on whether a user clicked button


is_page_view: Yes/No depending on whether a user loaded a new screen/page

is_scroll_up: Yes/No depending on whether a user scrolled up on the current screen

is_scroll_down: Yes/No depending on whether a user scrolled down on the current screen

timestamp: Timestamp at which the user action is captured

Here is an example of a JSON payload structure that is produced:

{
  "customer_id": 342516,
  "app_version": "4.1.34-beta",
  "OS_version": "Android",
  "lat": 14.11,
  "lon": 89.88,
  "page_id": "e7bc5fb2-1231-11eb-adc1-0242ac120002",
  "button_id": "fcba68aa-1231-11eb-adc1-0242ac120002",
  "is_button_click": yes,
  "is_page_view": no,
  "is_scroll_up": no,
  "is_scroll_down": no,
  "timestamp": "2020-10-20TT17:52:24"
}

### Architecture and Approach
The following diagram will help you understand what the entire architecture looks like 
![image](https://github.com/Mohammed-Zeeshaan-Saqeeb/Data_Capture_and_Analysis_of_Cab_rides/assets/77388869/28116a67-a6d8-45c1-8abe-ac71a9421040)

The two types of data are the clickstream data and the batch data. The clickstream data is captured in Kafka. The streaming framework consumes the data from Kafka and loads the same to Hadoop. Once the clickstream data enters the Stream processing layer, it is synced to the HDFS directory to process further. For the batch data, which is the bookings data, the data is stored in the RDS and ingested to Hadoop. 

Also, in cases wherein aggregates need to be prepared, data is read from HDFS, processed by a processing framework such as Spark and written back to HDFS to create a Hive table for the aggregated data.

Once both the data are loaded in HDFS, this data is loaded into Hive tables to make it queryable. Hive tables serve as final consumption tables for end-user querying and are eventually consistent.

### Tasks
As part of this project, the following tasks are performed:

Task 1: Write a job to consume clickstream data from Kafka and ingest it into Hadoop.

Task 2: Write a script to ingest the relevant bookings data from AWS RDS to  Hadoop.

Task 3: Create aggregates for finding date-wise total bookings using the Spark script.

Task 4: 

Create a Hive-managed table for clickstream data.

Create a Hive-managed table for booking data.

Create a Hive-managed table for aggregated data in Task 3.

Task 5: Calculate the total number of different drivers for each customer.

Task 6: Calculate the total rides taken by each customer.

Task 7: Find the total visits made by each customer on the booking page and the total ‘Book Now’ button presses. This can show the conversion ratio.
The booking page ID is ‘e7bc5fb2-1231-11eb-adc1-0242ac120002’.
The Book Now button ID is ‘fcba68aa-1231-11eb-adc1-0242ac120002’. You also need to calculate the conversion ratio as part of this task. Conversion ratio can be calculated as Total 'Book Now' Button Press/Total Visits made by customers on the booking page.

Task 8: Calculate the count of all trips done on black cabs.

Task 9: Calculate the total amount of tips given date wise to all drivers by customers.

Task 10: Calculate the total count of all the bookings with ratings lower than 2 as given by customers in a particular month.

Task 11: Calculate the count of total iOS users.


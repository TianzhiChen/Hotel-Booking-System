# Hotel-Booking-System
This hotel booking application will allow customers to find and book a hotel room based on room availability. This hotel booking application design utilizes AWS to ensure scalability, high availability, and security, enhancing the user experience while optimizing operational efficiency

## Architecture Diagram

![Image](https://github.com/TianzhiChen/Hotel-Booking-System/blob/main/images/Hotel%20Booking-AWS.jpg)

## Application Features
- Hotel Admin：
  - Hotels CRUD
  - Rooms CRUD
  - Bookings management
  - Business Insights
- Users:
  - Search for hotels/rooms
  - Make reservations 

## Hotel & Room Listing Service
- Hotel booking systems often require storing and managing a vast amount of data, including hotel information, room details, reservation records, and user profiles. In such cases, we use Amazon RDS (Relational Database Service) to store and manage the databases.
- Each hotel and room will come with a lot of media resources like images and videos, Amazon S3 is suitable for storing static resources and object data.
- Amazon CloudFront is a content delivery network (CDN) used to accelerate the delivery of static resources such as images and videos to users across various global locations, thereby enhancing the user experience.

<img src="https://github.com/TianzhiChen/Hotel-Booking-System/blob/main/DB/Hotel_Room_DB.jpg" width="400">

## Search Service
- Fuzzy Search and Typeahead Search are essential to the user experience. Elasticsearch is better suited and much more frequently used for time-series data use cases, like log analysis use cases.
    - Elasticsearch can quickly return relevant results when users search for specific hotels or room information using keywords.
    - Elasticsearch supports real-time index updates, ensuring that when new hotel or room information is added to the system, the search index is promptly updated, enabling users to instantly access the latest results.
    - For a Hotel Booking system, which might have thousands of hotels and rooms to search through, Elasticsearch can efficiently process these requests.
    - Elasticsearch offers robust querying and filtering capabilities, which are essential for Hotel Booking systems that often require support for intricate queries, including range queries, filtering, sorting, and more.

## Booking Service
- The Booking Service is a CRUD service responsible for maintaining a table of all bookings. It requires storing and managing a substantial volume of data, including information such as user_id, room_id, quantity, start_date, end_date, and more. In such scenarios, Amazon RDS (Relational Database Service) is employed to store and manage the databases.
- We need to check the availability before inserting a new booking record. One way to do that is maintaining a table just for available room count which contains room_id, date, available_count, and available_count can be a row-level constraint that is always non-negative. After the verification, we can then proceed to create a new booking record.
- we can use a cache with a time-to-live(TTL) feature such as Amazon ElastiCache to handle overselling or incomplete transactions. Whenever a user started to check out, we "reserve" his booking in Amazon ElastiCache and set a TTL (e.g., 10 minutes) to temporarily deduct the count from the available_count. When the cache expires, we can add the count back to the available_count.
- A valid booking record should contain the following fields:
  
<img src="https://github.com/TianzhiChen/Hotel-Booking-System/blob/main/DB/Booking_DB.jpg" width="200">

and Status should be an Enum with these possible values: Reserved, Booked, Payment Declined, User Cancelled and Completed.

- The last step of the Booking Service will be sending a message to the Search engine and removing this room from the search results if it is fully booked.

## Analytics and Reporting Service
- When we have the supply and demand(from search trending and history information) data, we will be able to optimize the price for the hotel business. Therefore we feed all the search actions and order transactions to an Analytic Service which is a streaming service.
- We can also provide business insights and reports to hotel managers which can be used to drive their growth or for accounting purposes.

## Summary 
### AWS services utilized in the Hotel Booking System:
1. Amazon Elastic Load Balancer (ELB) and Amazon EC2:
    - ELB ensures even distribution of incoming traffic across multiple EC2 instances, improving system availability and response times.
    - EC2 instances host various components of the Hotel Booking System, ensuring scalability and efficient resource allocation.

2. Amazon S3 and Amazon CloudFront:
    - Amazon S3 serves as a reliable and scalable storage solution for storing media resources like images and videos associated with hotel listings and rooms.
    - Amazon CloudFront acts as a content delivery network (CDN), caching and distributing content globally, resulting in faster loading times for users.

3. Amazon RDS:
    - Amazon RDS provides a managed relational database service, ensuring efficient storage, retrieval, and management of booking-related data.
    - The booking database maintains crucial information such as user IDs, room details, reservation dates, and more.

4. Amazon ElasticCache for Redis:
    - Amazon ElasticCache for Redis offers an in-memory caching solution, enhancing system performance by storing frequently accessed data.
    - This caching mechanism is vital for managing incomplete transactions and efficiently handling overselling scenarios.

5. Amazon Managed Streaming for Apache Kafka:
    - Amazon MSK provides a managed and scalable platform for deploying Apache Kafka clusters, enabling real-time event streaming and communication.
    - Kafka is employed for seamless synchronization of data and events across different parts of the Hotel Booking System, facilitating real-time updates and notifications.
    

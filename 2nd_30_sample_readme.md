
## **Lab Name : Object Oriented Programming Sessional**
## **Submission Date : August 26, 2025**
### **Group Members :**
| Name             | Roll        |
|------------------|-------------|
| MD. NAHID REZA   | 2310037     |
| MD. SHUVRA HASAN | 2310040     |
| MD. ARIFUL ISLAM | 2310044     |

---

<h2>1. Project Title</h2>
<p><strong>E-Ticket Booking System</strong></p>

<h2>2. Project Summary</h2>
<p>A C++-based console application for booking bus, train, and flight tickets, featuring route selection, premium options, fare calculation with discounts, passenger information management, multiple payment methods (Bkash, Nagad, Bank), and file-based storage for ticket details.</p>



### **Code:**

```C++

```

<h2>3. Program Functionality</h2>
<p>The <strong>Ticket Booking System</strong> is a console-based C++ application designed to streamline ticket reservations for various travel modes. Its key functionalities include:</p>
<ul>
    <li>Allowing users to select ticket types (bus, train, or flight), routes, and premium or regular options.</li>
    <li>Calculating fares based on distance (bus/train) or fixed rates (flight), with discounts via coupon codes or first-customer offers.</li>
    <li>Managing passenger details (name, age, contact) with input validation.</li>
    <li>Processing payments through Bkash, Nagad, or Bank with transaction ID generation.</li>
    <li>Storing ticket and passenger information in a file and retrieving details by ticket ID.</li>
    <li>Providing a menu-driven interface for buying or checking tickets.</li>
</ul>
<p>The system leverages object-oriented programming (OOP) principles for modularity, extensibility, and maintainability.</p>

<h2>4. Implementation of OOP Concepts</h2>

<h3>4.1 Encapsulation</h3>
<p><strong>Definition</strong>: Bundles data and methods into classes, restricting access using private/protected specifiers and exposing functionality via public methods.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong><code>Passenger</code> Class</strong>: Private data (<code>name</code>, <code>age</code>, <code>contact</code>) with public methods (<code>setInfo()</code>, <code>displayInfo()</code>, <code>getAge()</code>, <code>getName()</code>). Location: Lines 59–79, 128–177.</li>
    <li><strong><code>Ticket</code> Class</strong>: Protected data (<code>ticketType</code>, <code>fare</code>, etc.) with public methods (<code>selectRoute()</code>, <code>showTicketInfo()</code>). Location: Lines 81–110, 193–263.</li>
    <li><strong>Purpose</strong>: Ensures data integrity (e.g., validates age/contact) and restricts direct access.</li>
</ul>

<h3>4.2 Inheritance</h3>
<p><strong>Definition</strong>: Derived classes inherit properties/methods from a base class for code reuse.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Base Class</strong>: <code>Ticket</code> (Lines 81–110) with common attributes/methods.</li>
    <li><strong>Derived Classes</strong>: <code>BusTicket</code>, <code>TrainTicket</code>, <code>FlightTicket</code> (Lines 112–131) override <code>calculateFare()</code> and <code>setTime()</code>.</li>
    <li><strong>Usage</strong>: <code>buyTicket()</code> (Lines 512–548) creates derived class objects via <code>Ticket*</code>.</li>
    <li><strong>Purpose</strong>: Shares common functionality while allowing specialized logic.</li>
</ul>

<h3>4.3 Polymorphism</h3>
<p><strong>Definition</strong>: Enables methods to behave differently based on the object. Includes compile-time (overloading) and run-time (virtual functions) polymorphism.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Compile-Time (Function Overloading)</strong>: <code>Ticket::applyDiscount</code> overloaded for percentage (Line 195) and fixed amount (Line 199). Called in <code>checkDiscount()</code> (Line 204).</li>
    <li><strong>Run-Time (Virtual Functions)</strong>: Pure virtual functions <code>calculateFare()</code> and <code>setTime()</code> in <code>Ticket</code> (Lines 104–105), implemented in derived classes (Lines 267, 302, 349, 269–294, 304–326, 351–372). Used via <code>Ticket*</code> in <code>buyTicket()</code> (Lines 512–548).</li>
    <li><strong>Purpose</strong>: Supports flexible discount application and dynamic method resolution.</li>
</ul>

<h3>4.4 Abstraction</h3>
<p><strong>Definition</strong>: Hides implementation details, exposing only essential functionality via abstract classes.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Abstract Class</strong>: <code>Ticket</code> with pure virtual functions <code>calculateFare()</code> and <code>setTime()</code> (Lines 104–105).</li>
    <li><strong>Usage</strong>: Implemented in <code>BusTicket</code>, <code>TrainTicket</code>, <code>FlightTicket</code> (Lines 267, 302, 349, 269–294, 304–326, 351–372).</li>
    <li><strong>Purpose</strong>: Ensures each ticket type defines its own logic while maintaining a common interface.</li>
</ul>

<h3>4.5 Diamond Problem</h3>
<p><strong>Definition</strong>: Occurs in multiple inheritance with a shared base class, resolved using virtual inheritance.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Setup</strong>: <code>Payment</code> (Lines 407–416) is inherited by <code>Bkash</code>, <code>Nagad</code>, <code>Bank</code> (Lines 418–436), which are inherited by <code>PassengerPayment</code> (Lines 438–458).</li>
    <li><strong>Solution</strong>: Virtual inheritance in <code>Bkash</code>, <code>Nagad</code>, <code>Bank</code> (Lines 418, 425, 432) ensures one <code>Payment</code> instance. Used in <code>makePayment()</code> (Line 454).</li>
    <li><strong>Purpose</strong>: Avoids ambiguity in accessing <code>Payment</code> members.</li>
</ul>

<h3>4.6 Error Handling</h3>
<p><strong>Definition</strong>: Uses try–catch blocks within while loops to manage exceptions robustly.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong><code>Passenger::setInfo()</code></strong>: Uses separate <code>while</code> loops to validate <code>name</code> (via <code>cin.fail()</code>), <code>age</code> (1–120), and <code>contact</code> (11 digits, starts with "01") with <code>try-catch</code> blocks (Lines 128–177).</li>
    <li><strong><code>Ticket::selectRoute()</code></strong>: Validates route choice within a <code>while</code> loop, using <code>try-catch</code> and <code>cin.clear()</code> for invalid inputs (Lines 211–242).</li>
    <li><strong><code>BusTicket::setTime()</code>, <code>TrainTicket::setTime()</code>, <code>FlightTicket::setTime()</code></strong>: Use <code>while</code> loops with <code>try-catch</code> to validate time choices, requiring valid input without default fallbacks (Lines 269–294, 304–326, 351–372).</li>
    <li><strong><code>buyTicket()</code></strong>: Validates ticket type choice within a <code>while</code> loop using <code>try-catch</code> and <code>cin.clear()</code> (Lines 512–548).</li>
    <li><strong>Purpose</strong>: Ensures robust input validation by looping until valid input is provided, handling errors gracefully without recursion or default fallbacks.</li>
</ul>

<h3>4.7 File Handling</h3>
<p><strong>Definition</strong>: Reads/writes data to files for persistent storage.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Writing</strong>: <code>FileSystem::storeTicketInformation</code> (Lines 372–388) appends ticket/passenger data to <code>buyer_details.txt</code>.</li>
    <li><strong>Reading</strong>: <code>displayBuyerDetailsByTicketID</code> (Lines 390–426) retrieves and displays ticket details.</li>
    <li><strong>Purpose</strong>: Persists booking data and enables retrieval by ticket ID.</li>
</ul>

<h3>4.8 Constructor & Destructor</h3>
<p><strong>Definition</strong>: Constructors initialize objects; destructors clean up resources.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong>Constructors</strong>: <code>Passenger</code> (Line 128), <code>Ticket</code> (Line 193), <code>BusTicket</code>/<code>TrainTicket</code>/<code>FlightTicket</code> (Lines 265, 300, 347), <code>Payment</code> (Line 410), <code>PassengerPayment</code> (Line 441).</li>
    <li><strong>Destructor</strong>: Virtual destructor in <code>Payment</code> (Line 415) for proper cleanup.</li>
    <li><strong>Purpose</strong>: Initializes objects and ensures safe resource deallocation.</li>
</ul>

<h3>4.9 Static Members</h3>
<p><strong>Definition</strong>: Shared class-level members across all instances.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong><code>Ticket::totalTicketsSold</code></strong>: Tracks ticket sales (Lines 84, 189, 255, 263).</li>
    <li><strong>Purpose</strong>: Manages first-customer discount and ticket count.</li>
</ul>

<h3>4.10 Friend Function/Class</h3>
<p><strong>Definition</strong>: Grants access to private/protected members.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong><code>FileSystem</code></strong>: Friend of <code>Passenger</code> (Line 74) and <code>Ticket</code> (Line 108) to access private/protected data in <code>storeTicketInformation</code> (Lines 372–388).</li>
    <li><strong>Purpose</strong>: Enables file storage without exposing data via getters.</li>
</ul>

<h3>4.11 Generic Class/Function</h3>
<p><strong>Definition</strong>: Uses templates for reusable code across data types.</p>
<p><strong>Implementation</strong>:</p>
<ul>
    <li><strong><code>roundUp</code></strong>: Template function rounds numbers to a multiple (Lines 52–56, 122–125, used in Lines 250, 257).</li>
    <li><strong>Purpose</strong>: Ensures consistent fare rounding.</li>
</ul>

<h2>5. Summary Table</h2>
<table>
    <tr>
        <th>Topic</th>
        <th>Key Locations (Lines)</th>
        <th>Key Classes/Functions</th>
    </tr>
    <tr>
        <td>Encapsulation</td>
        <td>59–79, 81–110, 128–177, 193–263</td>
        <td><code>Passenger</code>, <code>Ticket</code></td>
    </tr>
    <tr>
        <td>Inheritance</td>
        <td>81–110, 112–131, 512–548</td>
        <td><code>Ticket</code>, <code>BusTicket</code>, <code>TrainTicket</code>, <code>FlightTicket</code></td>
    </tr>
    <tr>
        <td colspan="3"><strong>Summary of OOP Implementation</strong></td>
    </tr>
    <tr>
        <td>Polymorphism (Compile-Time)</td>
        <td>195–202, 204</td>
        <td><code>applyDiscount</code></td>
    </tr>
    <tr>
        <td>Polymorphism (Run-Time)</td>
        <td>104–105, 267, 302, 349, 269–294, 304–326, 351–372, 512–548</td>
        <td><code>calculateFare</code>, <code>setTime</code></td>
    </tr>
    <tr>
        <td>Abstraction</td>
        <td>81–110, 104–105, 267, 302, 349, 269–294, 304–326, 351–372</td>
        <td><code>Ticket</code></td>
    </tr>
    <tr>
        <td>Diamond Problem</td>
        <td>407–458, 418, 425, 432, 454</td>
        <td><code>Payment</code>, <code>Bkash</code>, <code>Nagad</code>, <code>Bank</code>, <code>PassengerPayment</code></td>
    </tr>
    <tr>
        <td>Error Handling</td>
        <td>128–177, 211–242, 269–294, 304–326, 351–372, 512–548</td>
        <td><code>setInfo</code>, <code>selectRoute</code>, <code>setTime</code>, <code>buyTicket</code></td>
    </tr>
    <tr>
        <td>File Handling</td>
        <td>372–426</td>
        <td><code>storeTicketInformation</code>, <code>displayBuyerDetailsByTicketID</code></td>
    </tr>
    <tr>
        <td>Constructor & Destructor</td>
        <td>128, 193, 265, 300, 347, 410, 419, 426, 433, 441, 415</td>
        <td><code>Passenger</code>, <code>Ticket</code>, <code>Payment</code>, derived classes</td>
    </tr>
    <tr>
        <td>Static Members</td>
        <td>84, 189, 255, 263</td>
        <td><code>Ticket::totalTicketsSold</code></td>
    </tr>
    <tr>
        <td>Friend Function/Class</td>
        <td>74, 108, 372–388</td>
        <td><code>FileSystem</code></td>
    </tr>
    <tr>
        <td>Generic Class/Function</td>
        <td>52–56, 122–125, 250, 257</td>
        <td><code>roundUp</code></td>
    </tr>
</table>

<h2>6. Conclusion</h2>
<p>The <strong>Ticket Reservation System</strong> effectively implements all specified OOP concepts, including encapsulation, inheritance, polymorphism, abstraction, and virtual inheritance to resolve the diamond problem. It ensures robustness with enhanced error handling using while loops, persists data via file handling, and enhances functionality with static members and generic functions. The modular, reusable design makes it a strong example of OOP principles in action.</p>

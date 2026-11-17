# DriveShare – Video Script (30+ minutes)

About 32–35 minutes in total. **[DO]** lines are actions on screen. Everything else is what you say. Say it in your own words; you don't need to read it word for word. Speak at a relaxed pace and pause briefly after each action so viewers can see the result.

---

## Before you hit record

1. In a terminal, in the `DriveShare` folder, run:
   - `python seed.py` (resets the demo data)
   - `python run.py`
2. Open **two browser windows** side by side:
   - **Window A**, a normal window, for **Alice** (the owner)
   - **Window B**, a private/incognito window, for **Bob** (the renter)
   Both go to http://127.0.0.1:5000.
3. Demo logins:
   - Users: `alice@driveshare.test`, `bob@driveshare.test`, `carol@driveshare.test`
   - Password for all: `Password123`
   - Security answers: `blue`, `ann arbor`, `smith`
4. Have the slides open on the title slide, and VS Code open on the project.
5. Timing guide:
   - Part 1, Introduction: 2 min
   - Part 2, Project tour and setup: 2 min
   - Part 3, Live demo: 13 min
   - Part 4, Architecture: 2 min
   - Part 5, The six design patterns in code: 9 min
   - Part 6, Database: 2 min
   - Part 7, Testing: 2 min
   - Part 8, Design decisions and future work: 2 min
   - Part 9, Task summary and closing: 1 min

---

## Part 1 – Introduction (≈ 2 min)

**[DO]** Show the title slide.

> Hi, my name is Dheyaa Alrabeei. This is my term project for the software design course: **DriveShare**, a peer-to-peer car rental platform.
>
> The idea is similar to Turo. Many people own a car that sits in a driveway most of the week, and many other people only need a car for a weekend trip, a move, or a few days while their own car is in the shop. Traditional rental companies sit in the middle. DriveShare connects the two sides directly: owners list their own cars and earn money from them, and renters find a car nearby, often for less, and book it directly from the owner.
>
> I worked on this project individually. I implemented every required feature: registration with security questions, car listing and management, search and booking, watching cars, messaging and notifications, and payment. I also implemented the two features that were optional for individual students, rental history and reviews.
>
> On the design side, the project uses the six design patterns from the assignment: Singleton, Observer, Mediator, Builder, Proxy, and Chain of Responsibility.

**[DO]** Show the "What this video covers" slide.

> Here's the plan for this video. First, I'll give a quick tour of the project and how to run it. Then I'll do a complete live demo of every feature, using two users at the same time, an owner and a renter, so you can see how their actions affect each other. After that I'll explain the architecture, walk through all six design patterns in the source code, show the database design and the automated tests, and finish with my design decisions, what I would improve, and a summary of the tasks.

---

## Part 2 – Project tour and setup (≈ 2 min)

**[DO]** In VS Code, expand the project folders in the sidebar.

> This is the project in VS Code. It's a Python web application built with **Flask**, a lightweight web framework, and **SQLite**, a database stored in a single file, so there's nothing extra to install or configure.
>
> The main package is `driveshare`. Inside it:
> - `patterns` holds one file per design pattern, so each pattern is easy to find and to grade.
> - `services` holds the business logic: authentication, cars, bookings, community features like messaging and reviews, and notifications.
> - `routes.py` connects web addresses to those services.
> - `templates` holds the HTML pages, and `static` holds the stylesheet and the JavaScript, including `mediator.js`, which is the Mediator pattern.
>
> There's also a `tests` folder with the automated tests, and a `docs` folder with the design report, the class diagrams, the database diagram, screenshots, and these slides.

**[DO]** Open `README.md`, then switch to the terminal and point at the two commands.

> To run the project, you install the requirements, run `seed.py` to load demo data, and run `run.py` to start the server. The seed script creates three demo users, Alice and Carol who own cars and Bob who rents them, plus five cars and one past rental with reviews, so the history and review pages have something to show from the start. I've already started the server, so let's go to the browser.

---

## Part 3 – Live demo (≈ 13 min)

### 3.1 Home page as a guest

**[DO]** In Window A (not logged in), show the home page. Scroll to "Top-rated cars". Click **How it works**.

> This is the DriveShare home page. A guest can search by location and dates right from this banner, and below it are the top-rated cars, sorted by the owner's average review rating.
>
> The "How it works" page gives a short overview of the six design patterns, and it also shows how many active login sessions the Singleton session manager is holding right now. It's zero, because nobody is logged in yet.

### 3.2 Registration with three security questions

**[DO]** Click **Sign up**. Enter a name and email, the password `abcdefgh` (letters only) in both fields, three answers. Submit.

> Let's create an account. The form asks for name, email and password, plus **three security questions** and their answers. The user picks each question from a list, and the three must be different.
>
> First I'll try a weak password with only letters. The server rejects it with a clear message: a password must have at least eight characters and contain both a letter and a number. Every rule is checked on the server, not just in the browser, so it can't be bypassed.

**[DO]** Enter a valid password, e.g. `Demo12345`, in both fields. Submit.

> With a valid password the account is created and I'm logged in automatically. In the top bar you can see the navigation for a logged-in user, a notification bell, and my simulated wallet balance. Every new user starts with $1,000 so they can try the payment feature.
>
> Two security notes. First, the password and the three answers are never stored as plain text. They're stored as salted hashes, so even someone who stole the database file couldn't read them. Answers are also normalised, trimmed and lower-cased, so "Ann Arbor" and "ann arbor" count as the same answer.
>
> Second, logging in is handled by the **SessionManager**, the Singleton. It creates a long random token for this browser session and stores only a hash of it. The browser keeps the token in a signed cookie that JavaScript can't read.

**[DO]** Click **Log out**. Log in as Alice with a wrong password first, then the right one.

> If I enter the wrong password, the message just says "invalid email or password". It doesn't say which one was wrong, so an attacker can't use the login form to find out which emails have accounts. With the correct password, Alice is logged in.

### 3.3 Listing a new car (Builder)

**[DO]** As Alice, click **List your car**.

> Alice is a car owner. To list a car, she fills in this form. The fields marked with a star are required: make, model, year, mileage, price per day, and the pick-up location. Everything under "Optional details" can be left blank: car type, seats, transmission, fuel type, features, and a description.
>
> At the bottom is the **availability calendar**. The owner adds the periods when the car can be rented, each with a start and an end date and time. The form has room for three windows, and the owner can add more later.

**[DO]** Fill in: Honda, Accord, 2022, 15000, $60, Ann Arbor MI; type Sedan, 5 seats, Automatic; features "Bluetooth, Heated seats"; one availability window starting tomorrow and ending in three weeks. Click **Publish listing**.

> When I click Publish, the server builds the listing with the **CarListingBuilder**, the Builder pattern. Each field is set with its own method, and each method validates its own input: the year must be realistic, mileage can't be negative, the price must be positive, and every availability window must end after it starts. If anything is wrong, the owner sees a specific message. I'll show that code later.

### 3.4 Managing a listing

**[DO]** On the manage page, point at each card: Price, Add availability, Availability calendar, Booked periods.

> After publishing, Alice lands on the management page for her new car. From here she can:
> - change the daily price,
> - add new availability windows,
> - remove existing ones,
> - see which periods are already booked,
> - edit all the details, or unlist the car to hide it from search without deleting its history.

**[DO]** Click **Unlist**. Point at the flash message. Then click **Relist**.

> Unlisting hides the car from search immediately, and relisting brings it back. That's useful when the owner needs the car for themselves for a while.

**[DO]** Click **My cars** to show all four of Alice's listings.

> "My cars" shows all of Alice's listings with their price and status. She now has four cars: the three from the demo data and the Accord we just listed.

### 3.5 Searching (Mediator)

**[DO]** Switch to Window B. Log in as **Bob**. Click **Browse cars**.

> Now let's switch to the renter. This is Bob's window. Note his balance is $835, because he already paid for one past rental from the demo data. Bob opens the search page. There are seven filters on the left, and right now all five demo cars are shown.

**[DO]** Type "Ann" in Location. Pause. Then pick a pick-up 4 days from now and a drop-off 6 days from now.

> As I type a location, the results update **instantly**, with no search button and no page reload. Now only the cars in Ann Arbor are shown. When I add pick-up and drop-off dates, the search checks real availability: a car only appears if the owner offered it for that whole period and nobody has booked any part of it.

**[DO]** Clear Location. Set Car type to **SUV**, then Min seats **7+**. Then reset both and type "carplay" in Keyword. Then change Sort to **Price: high to low**.

> The other filters work the same way. Filtering by SUV and seven seats leaves only the RAV4 Hybrid. The keyword filter searches make, model and features, so "carplay" finds every car that lists Apple CarPlay. And I can sort by price, by year, or by the owner's rating.
>
> The page also updates its address bar as I filter, so the search can be bookmarked or shared.
>
> Behind this page is the **Mediator** pattern. The filter form, the result list and the result counter are three separate components. None of them knows the others exist. When a filter changes, the form only tells the **SearchMediator** "the filters changed". The mediator asks the server for new results, then tells the list to redraw and the counter to update. I'll show that code later too.

### 3.6 Car details page

**[DO]** Clear the filters and open the **Toyota Camry**. Scroll slowly through the page.

> This is the details page for Alice's Camry. It shows the year, make and model, location, mileage, the owner with her average rating, and all the optional details the Builder captured: type, seats, transmission, fuel and features.
>
> Lower down is the **availability calendar**: the green rows are the periods Alice offers, and the red rows are periods that are already booked. At the bottom are the reviews of Alice from previous renters, including Bob's review from the demo data.
>
> On the right is the booking panel and the watch form, and a button to message the owner.

### 3.7 Watching a car (Observer)

**[DO]** Open the **Tesla Model 3** ($120/day). In "Watch this car", enter target price **100**. Click **Watch car**.

> Bob would love to rent this Tesla, but $120 a day is more than he wants to pay. Instead of checking every day, he can **watch** the car. A watch has two kinds of conditions, and he can set either or both:
> - a **target price**: tell me when the price is at or below this amount;
> - **dates**: tell me when the car is free for these exact dates.
>
> I'll set a target price of $100 a day and save. The page confirms the watch is saved and will notify Bob when it matches. When a watch is created, DriveShare checks it once right away, so if the car already matched, Bob would get a notification immediately.

**[DO]** Click **Watchlist** in the top bar.

> The watchlist page shows every car Bob is watching: the current price, his target, the dates he needs, and the last time he was alerted. He can remove a watch at any time. We'll come back here after Alice changes her price.

### 3.8 Booking a car with a live quote

**[DO]** Open the **Toyota Camry**. Pick a pick-up 2 days from now at 10:00 and a drop-off 2 days after that.

> Now Bob books the Camry. As soon as he picks both dates, the booking panel shows a live price quote: 2 days times $55 is $110. The price counts every started 24-hour period as a full day, with a minimum of one day.
>
> At the same moment, the **Request to book** button becomes active and shows the total. The watch form below also copies the same dates in. That's the second mediator, the **BookingMediator**. The date pickers, the price quote, the Book button and the watch form are four separate components, and the mediator coordinates all of them.

**[DO]** Click **Request to book**. Show the Trips page that opens.

> After clicking Request to book, Bob lands on his trips page. The booking is **pending**. It's waiting for the owner to accept it. Bob also got a notification confirming the request was sent.

### 3.9 Preventing double booking

**[DO]** Go back to the Camry. Pick dates that overlap the booking you just made, e.g. pick-up 3 days from now, drop-off 5 days from now.

> One of the key requirements is that **the same car can never be rented by two people for overlapping times**. Let me try to book the Camry again for dates that overlap the booking I just made.
>
> The panel immediately says the car is already booked for part of that period. The Book button is disabled, and the watch form is highlighted, suggesting Bob watch the car instead in case that booking falls through.

**[DO]** Pick a pick-up exactly when the first booking ends, and a drop-off a day later.

> Back-to-back bookings are allowed, though. If the new booking starts exactly when the previous one ends, there's no overlap, and the quote is accepted.
>
> This rule isn't only in the browser. On the server, a booking is created inside a database transaction that takes a write lock, then checks for overlaps and inserts the booking in one step. So even if two renters click Book at the exact same moment, one succeeds and the other gets an error. As a final safety net there's also a trigger in the database itself that refuses any overlapping booking, even if a bug in the application code skipped the check.

### 3.10 Owner notifications, confirm and decline

**[DO]** Switch to Window A (Alice). Point at the bell badge. Click the bell.

> Back in Alice's window, the bell shows a red badge. It updates by itself every few seconds, and a small pop-up message appears when something new arrives. Alice opens her notifications: "New booking request from Bob Smith for your Toyota Camry", with the dates and the total.
>
> Every notification is also sent as an email. Since this is a demo, the emails are written to a file called the email outbox instead of being sent. I'll show that file in a minute.

**[DO]** Click **Trips & history**, then the **As owner** tab. Click **Confirm** on Bob's Camry booking.

> In Trips & history, the "As owner" tab lists every booking of Alice's cars, including the past rental from the demo data. Bob's request is pending, so Alice can confirm or decline it. She confirms it, and Bob is notified that the booking is confirmed and that he can now pay.

**[DO]** In Window B (Bob), book the **RAV4 Hybrid** for any free dates. In Window A (Alice), refresh the owner tab and click **Decline** on it.

> Let me also show the decline path. Bob requests the RAV4 too, and this time Alice declines. Bob gets a "your request was declined" notification, and those dates become free again for other renters.

### 3.11 Cancelling a booking

**[DO]** In Window B, request the RAV4 again. Then on Bob's Trips page click **Cancel** on it.

> A renter can also cancel a booking while it's still pending or confirmed but not yet paid. Bob requests the RAV4 again and then changes his mind and cancels. Alice is notified of the cancellation.
>
> Declines and cancellations matter for the Observer pattern too. When dates are freed up, DriveShare checks every watch on that car. If another renter was waiting for exactly those dates, they're notified automatically. My automated tests cover that case.

### 3.12 A price drop triggers the watch (Observer)

**[DO]** In Window A (Alice), go to **My cars → Manage** for the Tesla. Change the price to **95** and click **Update price**.

> Now the watch. Alice decides to lower the Tesla's price from $120 to $95 a day. The message confirms the price is updated and that watchers have been checked.

**[DO]** Switch to Window B. Wait for the pop-up, then click the bell.

> And in Bob's window, a pop-up appears and the bell badge goes up. The notification reads: "Good news! The 2022 Tesla Model 3 you are watching: price is now $95.00 per day, your target was $100.00." If I open the watchlist, it now shows when he was last alerted.
>
> This is the **Observer pattern**. The car is the subject, and Bob's watch is an observer. Alice's price-change code doesn't know anything about Bob or his watch. It just announces "the price changed", and each watcher decides for itself whether its conditions are met.

### 3.13 Payment (Proxy)

**[DO]** In Window B, go to **Trips & history**. Point at the confirmed Camry booking and the **Pay $110.00** button. Point at Bob's balance ($835). Click **Pay**.

> Bob's Camry booking is confirmed, so a **Pay** button appears with the exact amount. A real payment system isn't required for this project, so clicking the button simulates the payment. Watch Bob's balance: it's $835 now.
>
> After the payment, the booking status is **paid**, the balance is $725, and the success message shows a payment reference number. The Pay button is gone, because a booking can only be paid once.

**[DO]** Show Bob's notification, then switch to Window A and point at Alice's balance and her new notification.

> Both sides are notified. Bob gets a payment receipt, and Alice gets "You received $110 from Bob Smith", and her balance went up by exactly $110.
>
> The payment goes through a **PaymentProxy**, the Proxy pattern. Before any money moves, the proxy checks that:
> - the person paying is logged in,
> - they are the renter on this booking,
> - the booking is confirmed and not already paid,
> - the amount matches the booking total, so it can't be changed in the browser,
> - the renter has enough balance.
>
> It also writes an audit log with the card number masked. Only when every check passes does it call the real payment gateway.

### 3.14 Messaging

**[DO]** In Window B, click **Message** on the Camry booking. Send: "Hi Alice! Where should I pick up the car on Friday?"

> Owners and renters can message each other from a car page, from a booking, or from a profile. Bob asks Alice about the pick-up. You can see the earlier messages in this conversation from the demo data. Each message also shows which car it's about.

**[DO]** Switch to Window A. Show the new-message pop-up or bell. Click **Messages**, open Bob's conversation, and reply "The driveway at 123 Main St, keys in the lockbox."

> Alice is notified about the new message. Her inbox shows each conversation with the latest message and a count of unread messages. She opens it and replies, and Bob gets notified the same way.

### 3.15 Rental history and reviews

**[DO]** In Window B, open **Trips & history → As renter**. Point at the past rental marked **completed** and the paid Camry. Leave a 5-star review with a comment on the Camry.

> The rental history shows all of Bob's trips with their status: pending, confirmed, paid, declined, cancelled, and **completed**, which is a paid rental whose end date has passed, like the one from the demo data.
>
> Once a booking is paid, **both sides can review each other**. Bob gives Alice five stars with a comment. Each person can review a rental only once. After reviewing, the button is replaced with "Reviewed".

**[DO]** In Window A, open **As owner** and review Bob. Then click Bob's name to open his profile.

> Alice reviews Bob as a renter from the owner tab. Clicking a name opens that user's profile, which shows their average rating, every review they've received, and their active listings. These ratings are what the "Top-rated cars" section and the rating sort use.

### 3.16 Email outbox

**[DO]** In VS Code, open `instance/email_outbox.log`.

> Here is the simulated email outbox. Every notification we've seen, the booking requests, confirmation, decline, cancellation, watch alert, payment receipts, messages and reviews, was also "emailed" to the right person, with the recipient, subject and message.

### 3.17 Password recovery (Chain of Responsibility)

**[DO]** In Window B, log out. Click **Log in → Forgot password?**. Enter `carol@driveshare.test` and click **Continue**.

> The last feature is password recovery. Carol forgot her password. First she enters her email. Then she sees her three security questions and fields for a new password.

**[DO]** Enter `blue`, `detroit`, `smith`, and `NewPass123` twice. Submit.

> If even one answer is wrong, like the second one here, the request is rejected. The message is deliberately vague: it doesn't say which answer was wrong, so an attacker can't guess the questions one at a time. Each failure is counted, and after five failures, recovery is locked for that account.

**[DO]** Enter `blue`, `ann arbor`, `smith`, and `NewPass123` twice. Submit. Log in as Carol with `NewPass123`.

> With all three correct answers, the password is reset and every existing session for Carol is ended, in case someone else was logged in as her. She can now log in with her new password.
>
> That completes the demo of every feature.

---

## Part 4 – Architecture (≈ 2 min)

**[DO]** Show the "Architecture" slide.

> Now let's look at how it's built. DriveShare has three layers.
>
> The **presentation layer** is in the browser: the HTML templates, the stylesheet, and `mediator.js`. The templates submit normal forms, and the mediator components call small JSON endpoints, for example to get a live price quote or search results.
>
> The **web layer** is `routes.py`. I kept the routes deliberately thin. A route reads the request, calls one service function, and either shows a page or redirects. Routes contain no business rules.
>
> The **service layer** is where the rules live. The auth service handles registration and login. The car service handles listings, prices, availability and search. The booking service handles availability checks, pricing and the booking life cycle. The community service handles watches, messages, payments and reviews. The notification service creates notifications and writes the email outbox.
>
> Underneath, the **patterns** folder has one file per pattern, and **database.py** defines the schema. One advantage of this separation is testing. The pattern classes don't depend on Flask or on the database, so I can unit-test them on their own. For example, the Observer gets its availability check and notifier passed in as functions.

---

## Part 5 – The six design patterns in code (≈ 9 min)

**[DO]** For each pattern, show its slide (class diagram) briefly, then open the file in VS Code.

### 5.1 Singleton – `driveshare/patterns/session_manager.py`

**[DO]** Open `session_manager.py`. Point at `_instance`, `_instance_lock` and `__new__`.

> The first pattern is the **Singleton**, used to manage user sessions securely. The problem it solves: every request needs to know who is logged in, and if different parts of the code each tracked sessions, they could disagree. Logout might work in one place and not another.
>
> The class keeps the single instance in a class variable, `_instance`. The trick is in `__new__`, which Python calls before `__init__`. If the instance doesn't exist yet, it takes a lock, checks again, and creates it. Otherwise it returns the existing one. This is called double-checked locking. The lock matters because Flask can handle several requests on different threads at the same time, and without it two threads could each create their own instance.

**[DO]** Scroll to `create_session`, `get_user_id` and `end_session`.

> `create_session` generates a 32-byte random token with Python's `secrets` module and stores only the SHA-256 hash of it, together with the user id and timestamps. `get_user_id` hashes the token it receives and looks it up. If the session has been idle for more than 30 minutes, it's removed and treated as logged out. Otherwise the timer is refreshed. `end_session` handles logout, and `end_all_sessions_for` logs a user out everywhere, which the password reset uses.

**[DO]** Open `services/auth_service.py` and show `login_user` and `load_current_user`.

> In the auth service you can see the Singleton in use. `SessionManager()` is called in several places, and it's always the same object.

### 5.2 Builder – `driveshare/patterns/car_builder.py`

**[DO]** Open `car_builder.py`. Show the `CarListing` dataclass, then `CarListingBuilder`.

> The second pattern is the **Builder**, used to create car listings. A listing has six required attributes and eight optional ones. A constructor with fourteen parameters would be hard to read and easy to call in the wrong order.
>
> `CarListing` is the **product**. It's a frozen dataclass, so once built it can't be changed by accident. `CarListingBuilder` builds it step by step. There's a method for every part, like `set_model`, `set_year`, `set_mileage`, `set_price_per_day`, and `add_availability`. Each method validates its own value and returns `self`, which lets the calls be chained in one readable expression.

**[DO]** Scroll to `build()`, then to `CarListingDirector`.

> `build` checks that every required part was set. If something's missing it raises an error naming the missing fields. Otherwise it returns the finished listing.
>
> There's also a **Director**, `CarListingDirector`, with recipes like `economy_sedan` and `family_suv` that preset the common options. The seed script uses it for two of the demo cars.

**[DO]** Open `services/car_service.py` and show `builder_from_form` and `save_listing`.

> In the car service, `builder_from_form` drives the builder from the submitted HTML form, and `save_listing` stores the finished product. Editing a listing runs the same builder again, so create and edit always share exactly the same validation.

### 5.3 Observer – `driveshare/patterns/observer.py`

**[DO]** Open `observer.py`. Scroll to `CarObserver` and `WatchObserver`.

> The third pattern is the **Observer**, which notifies renters when their watched cars meet their expectations. `CarObserver` is the abstract observer, with one method, `update`. `WatchObserver` is the concrete observer, and there's one for each active watch.
>
> Its `criteria_met` method checks two things. If the renter set a maximum price, the car's price must be at or below it. If the renter set dates, the car must be available for those dates. If any condition fails, nothing happens. `update` calls `criteria_met`, and only if it passes does it build a friendly message and call the notifier.

**[DO]** Scroll to `CarListingSubject`, then `CarEventPublisher`.

> `CarListingSubject` is the subject. It has the classic methods `attach`, `detach` and `notify_observers`, and `notify_observers` loops through the observers and calls `update` on each.
>
> Because watches are stored in the database, I added a `CarEventPublisher` to connect the two. Its `publish` method loads the car, creates a `WatchObserver` for every active watch on that car, attaches them to a subject, and fires the event.

**[DO]** Open `services/car_service.py`. Show `make_publisher` and `update_price`.

> Here, `update_price` saves the new price and then publishes a PRICE_CHANGED event. That's exactly what ran when Alice lowered the Tesla to $95. The same thing happens when an owner adds availability, and in the booking service when a booking is declined or cancelled. In each case, the code that changes the car never needs to know who is watching it.

### 5.4 Mediator – `driveshare/static/js/mediator.js`

**[DO]** Open `mediator.js`. Show the `Mediator` and `Component` base classes.

> The fourth pattern is the **Mediator**, used for the user interface. It's the only pattern written in JavaScript, because it coordinates components in the browser.
>
> The base `Mediator` class keeps a list of components and has a `notify` method. The base `Component` class has a reference to its mediator and one method, `emit`. Components only ever call `emit`. They never talk to each other directly.

**[DO]** Scroll to `DateRangePicker`, `PriceQuote`, `BookButton`, `WatchForm`, then `BookingMediator`.

> On the car page there are four components: the date picker, the price quote, the book button and the watch form. When a date changes, the date picker emits "datesChanged". The `BookingMediator` receives it, copies the dates into the watch form, and asks the server for a quote. If the car is available, it tells the quote to show the price and the button to enable itself. If not, it shows the reason, disables the button, and highlights the watch form.
>
> Without a mediator, the date picker would need references to the quote, the button and the watch form, and every new widget would add more connections. With the mediator, all the coordination lives in one place.

**[DO]** Scroll to `SearchMediator` and `NavMediator`, then the page wiring at the bottom.

> The `SearchMediator` coordinates the filters, results and counter that we saw in the demo. The `NavMediator` coordinates a poller, the notification bell and the pop-up: every few seconds the poller emits a tick, the mediator checks for new notifications, updates the bell, and shows the pop-up if the count went up. At the bottom, each page registers the components it has with the right mediator.

### 5.5 Proxy – `driveshare/patterns/payment_proxy.py`

**[DO]** Open `payment_proxy.py`. Show `PaymentGateway` and `SimulatedPaymentGateway`.

> The fifth pattern is the **Proxy**, used to simulate secure interaction with a payment system. `PaymentGateway` is the common interface, with one method, `process_payment`. `SimulatedPaymentGateway` is the real subject. It moves the money: it subtracts from the renter, adds to the owner, records the payment and marks the booking paid, all in one database transaction, so either everything happens or nothing does.

**[DO]** Scroll to `PaymentProxy`, `_validate` and `process_payment`.

> `PaymentProxy` implements the same interface, so the rest of the code can't tell it apart from the real gateway. Its `process_payment` method first calls `_validate`, which runs the security checks I listed in the demo: logged in, correct renter, confirmed and not already paid, exact amount, and enough balance. A rejected request is logged and returned as a failure. If everything passes, it logs the approval with the card number masked and delegates to the real gateway.
>
> The proxy also creates the real gateway only when it's first needed, in the `_gateway` method. That's the "virtual proxy" idea: an expensive or sensitive object is created only once a request has passed every check.

**[DO]** Open `services/community_service.py` → `pay_booking`.

> The community service only ever creates a `PaymentProxy`, never the real gateway. After a successful payment it sends the two notifications we saw.

### 5.6 Chain of Responsibility – `driveshare/patterns/password_recovery.py`

**[DO]** Open `password_recovery.py`. Show `RecoveryHandler`.

> The last pattern is **Chain of Responsibility**, used for password recovery. Recovery is a series of security checks that must all pass in order. Each check is a separate handler object.
>
> `RecoveryHandler` is the abstract handler. `set_next` links the next handler and returns it, so handlers can be chained in one line. `handle` runs this handler's own `check`. If the check returns a failure, the chain stops right there and the failure is returned. Otherwise the request moves to the next handler. If there's no next handler, every check has passed and the result is success.

**[DO]** Scroll through `AccountLockoutHandler`, `SecurityQuestionHandler`, `PasswordResetHandler`, then `build_recovery_chain`.

> There are three kinds of handler:
> - `AccountLockoutHandler` stops the chain if the account already has five failed attempts.
> - `SecurityQuestionHandler` is created three times, once per question, as the assignment asks. It verifies the answer against the stored hash, and if it's wrong it increases the failure counter.
> - `PasswordResetHandler` is the last link. It checks the new password is strong, saves its hash, resets the counter, and through the Singleton ends all of the user's existing sessions.
>
> `build_recovery_chain` links them in order: lockout, question 1, question 2, question 3, reset. To add a step later, like a code sent by email, I'd write one new handler class and add it to the chain, without changing any existing handler.

---

## Part 6 – Database (≈ 2 min)

**[DO]** Show the "Database" slide. Then open `driveshare/database.py` and scroll through the schema.

> The data is stored in SQLite, in ten tables. The schema is in `database.py`.
> - **users** stores the account, the password hash, the wallet balance and the recovery failure counter.
> - **security_questions** stores exactly three questions per user, with hashed answers.
> - **cars** stores each listing, with the optional fields allowed to be empty.
> - **car_availability** stores the periods the owner offers each car.
> - **bookings** stores each reservation with its dates, total and status.
> - **payments** has at most one payment per booking.
> - **watches** stores each renter's watch conditions for the Observer.
> - **notifications** and **messages** support communication.
> - **reviews** stores ratings, and each person can review a booking only once.
>
> The database enforces rules too, not just the code: unique emails, ratings from one to five, end times after start times, one payment per booking, and the `prevent_double_booking` trigger I mentioned earlier.

**[DO]** Scroll to the `prevent_double_booking` trigger.

> This is the trigger. Before any booking is inserted, it checks for an active booking of the same car where the start is before the new end and the end is after the new start. That's the standard overlap test. If one exists, the insert is aborted.

---

## Part 7 – Testing (≈ 2 min)

**[DO]** In the terminal, run `python -m pytest -q`. Wait for "19 passed". Then open `tests/test_patterns.py` and scroll.

> To make sure everything works, and keeps working after changes, I wrote 19 automated tests with pytest. Each test runs against a fresh temporary database. All 19 pass.
>
> The tests cover:
> - **Singleton:** two calls return the same object, and sessions can be created, looked up and ended.
> - **Builder:** missing required fields are rejected, Director presets work, and invalid availability windows are refused.
> - **Observer:** a price drop notifies the watcher, and a cancelled booking notifies someone waiting for those dates.
> - **Double booking:** overlapping bookings are refused at both the service level and the database-trigger level, and back-to-back bookings are allowed.
> - **Proxy:** payment is refused for a pending booking, the wrong user, the wrong amount, and a second payment, and the real gateway isn't created until a payment is valid.
> - **Chain of Responsibility:** the test checks that a wrong second answer fails at exactly question 2, that a correct request passes every link in order, and that five failures lock recovery.
> - **Security helpers and notifications:** password rules and answer normalisation handle unusual input, and notifications are written to the email outbox.
> - **End to end:** two tests drive the real web pages, one going from registration through password recovery to login, and one going from booking to confirmation, payment, reviews, messaging and notifications.

---

## Part 8 – Design decisions and future work (≈ 2 min)

**[DO]** Show the "Correctness and testing" slide.

> A few design decisions I want to highlight.
>
> **Security:** passwords and security answers are hashed with salts, session tokens are random and stored only as hashes, cookies are signed and hidden from JavaScript, redirects after login only go to pages on this site, and the password recovery hides which answer was wrong.
>
> **Reliability:** anything involving money or availability happens inside a database transaction, so the system can't end up half-updated, for example with the renter charged but the owner not paid.
>
> **Keeping the patterns clean:** each pattern lives in its own module and doesn't depend on Flask. The Observer and the Chain get their dependencies passed in, which made them easy to test.
>
> If I continued the project, I would add:
> - real email delivery instead of the outbox file,
> - car photos uploaded by owners,
> - a map view for pick-up locations,
> - a real payment provider, which the Proxy is designed to make easy: only the real gateway class would change,
> - an admin view for handling disputes between owners and renters.

---

## Part 9 – Task summary and closing (≈ 1 min)

**[DO]** Show the "Task summary" slide.

> Since I worked alone, I completed every task myself:
> - the requirements and design, including the class diagrams and the database diagram;
> - the database schema and demo data;
> - authentication, the Singleton session manager and the Chain of Responsibility password recovery;
> - car listings with the Builder, search, booking and double-booking prevention;
> - the watch list with the Observer, messaging and notifications;
> - payment with the Proxy, rental history and reviews;
> - the user interface and the Mediator JavaScript;
> - the tests, the design report, the slides and this video.

**[DO]** Show the "Thank you" slide.

> All of the source code, the design report with the class diagrams and database schema, the screenshots, and these slides are in my GitHub repository, linked on this slide and in my report.
>
> Thank you for watching.

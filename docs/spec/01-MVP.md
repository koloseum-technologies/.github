# KOLOSEUM

## Minimum Viable Product Design Documentation

Written by Davy Kamanzi  
(Head of Technology and Community Relations, Ace Pro Sports Technologies)

# Table of Contents

- [Introduction](#introduction)
    - [Background](#background)
- [Important context](#important-context)
    - [Objectives](#objectives)
    - [Visual presentation](#visual-presentation)
    - [Architecture](#architecture)
    - [User model](#user-model)
    - [Platform currency](#platform-currency)
    - [Sessions / Operations](#sessions--operations)
    - [Markets (Competitions)](#markets-competitions)
- [Public](#public)
    - [Landing](#landing)
    - [Authentication](#authentication)
    - [Help](#help)
    - [Legal](#legal)
- [Players](#players)
    - [General functionality](#general-functionality)
    - [Sessions](#sessions)
    - [KLSM](#klsm)
    - [Competitions](#competitions)
    - [Account](#account)
- [Lounges](#lounges)
    - [General functionality](#general-functionality-1)
    - [Operations](#operations)
    - [Staff](#staff)
    - [Account](#account-1)
- [Backroom](#backroom)
    - [General functionality](#general-functionality-2)
    - [Compliance](#compliance)
    - [KLSM](#klsm-1)
    - [Staff](#staff-1)

# Introduction

Koloseum is an online esports platform that allows individuals to **track gaming sessions at their local gaming lounge**, **find and participate in competitions across various games**, **shop for tickets and merchandise**, **trade used games and consoles**, and more.

Gaming lounges will use Koloseum to **track sessions and payments**, **manage daily operations**, and **participate in competitions**. With the prevalence of gaming lounges set up solely for casual gaming, existing esports organisations should be encouraged to merge or partner with gaming lounges and manage their competitive operations through the role-based access provided on the platform.

Koloseum staff will be provided a separate backend to **monitor users**, **manage shop offerings**, **track orders and payments**, **manage staff**, and more. Roles will be assigned to staff members (registered as Players) as appropriate for access.

## Background

Ace Pro Sports Technologies Limited (previously Ace Pro Gaming) began staging FIFA video game competitions in 2016 and gathering statistical data on the competitors and their interactions with the competitions, including registration price sensitivity, optimal competition formats and demographics (i.e. age and residential area).

After forming [TEKKEN 254](https://www.tekken254.co.ke/), founder Davy Kamanzi (now Head of Technology and Community Relations at Ace Pro Sports Technologies) began a working relationship with Ace Pro Gaming in 2017, which saw the company's efforts expand into the local fighting game community.

With over 8 years of market research under their belt, Ace Pro Sports Technologies is now ready to disrupt Kenyan esports and emerge as the market leader by taking industry standards to new heights with Koloseum.

# Important context

To keep this design documentation as concise as possible, **only specifications for the desktop version of the (web) app are included**; none are provided for any responsive design that will be done during the platform's development.

While reading through this design documentation, it will be helpful to keep the following information in mind.

## Objectives

The collection of microservices listed and described in this design documentation constitute a **proof of concept**, whose intended purpose is to enable us to begin:

1. **Generating revenue**
2. **Collecting and analysing market data**

The successful implementation of this proof of concept is expected to culminate in our primary objective of **raising capital through investment**.

## Visual presentation

Ace Pro Sports Technologies co-founder Fanuel Opiyo (Head of Business and Leagues) previously suggested creating a **"sports finance" vibe** for the platform, with the financial aspects of esports highlighted. This is an interesting approach that will be pursued for the platform's visual design; however, **any design decisions and implementations must remain focused on the current microservice and its purpose.**

## Architecture

The platform will be **split into various microservices** serving different purposes, **grouped into four user categories**. Each category and its microservices are discussed in their own sections.

**Public**

- Landing
- Authentication
- Help
- Legal

**Players**

- Sessions
- Competitions
- KLSM
- Account

**Lounges**

- Operations
- Staff
- Account

**Backroom**

- Compliance
- KLSM
- Staff

### Why microservices?

To meet increased traffic needs, each microservice can be **scaled horizontally**. This means that **replicas of each** microservice **can be created to share the load of incoming requests** with the help of a load balancer, which is a special server that redirects incoming requests to particular replicas using one of various methods; one such method is round-robin, which involves cycling through each replica sequentially with each incoming request.

This form of scaling is **more flexible than vertical scaling**, which refers to upgrading resources on a single microservice and can only happen up to a certain limit. It also **eliminates a single point of failure** in that if one replica fails, the others can continue to receive requests. This **helps maintain customer satisfaction** as the platform continues running without interruption, while **savings are also made on time and money** that would be spent on server repairs and upgrades, and money that would be lost from users unable to access the platform during downtime.

With this logic in mind, it can be expected in the long run that of the four aforementioned user categories, **Player microservices will scale the furthest** as they are expected to serve a large majority of our user base, while **Backroom microservices may only need to scale as the number of Koloseum staff increases**. This information will be useful in drawing up future financial projections.

**Each microservice** in Koloseum **will be packaged as a container.** A container is a standardised unit of software that packages code and all its dependencies so that it can run consistently across different environments. This makes it easy to deploy and manage microservices, as they can be easily moved from one environment to another without any changes.

### Deployment with Kubernetes

**Kubernetes** (_koo-buh-net-eez_; also known as **K8s**) is an open-source container orchestration system for automating software deployment, management, and scaling. It allows for the efficient use of hardware resources by grouping containers into logical units called pods and scheduling them across a cluster of nodes.

Key features that make Kubernetes a suitable choice for handling Koloseum's deployment include:

- **Scalability:** Kubernetes can easily scale Koloseum's microservices horizontally to handle increased traffic, ensuring uninterrupted platform operation.
- **High availability:** If a replica fails, Kubernetes will automatically reroute requests to other replicas, maintaining customer satisfaction and preventing downtime.
- **Flexibility:** Unlike vertical scaling, Kubernetes enables horizontal scaling by creating replicas of each microservice, providing more flexibility and cost-effectiveness.
- **Load balancing:** Kubernetes uses load balancers to distribute incoming requests evenly across replicas, optimising resource utilisation and preventing bottlenecks.
- **Networking and service discovery:** Kubernetes provides advanced networking and service discovery features, allowing Koloseum's microservices to communicate seamlessly.
- **Deployment flexibility:** Kubernetes supports various deployment strategies, allowing for gradual rollouts, canary deployments, and blue/green deployments.
- **Multi-cloud support:** Kubernetes can run on various cloud providers and on-premises environments, providing flexibility, and avoiding vendor lock-in.
- **Advanced security and access control:** Kubernetes offers robust security features, including role-based access control (RBAC), network policies, and pod security policies.

While services like GitHub Pages, Cloudflare Pages, and Netlify offer simplicity and ease of use, especially for static websites and smaller projects, they may not match the scalability, customisation, and extensibility that Kubernetes provides for complex applications like Koloseum due to the following limitations:

- **Microservices architecture:** These services may not efficiently handle the complexities of microservices architectures, which are commonly used in large-scale applications like Koloseum. Kubernetes, with its built-in support for service discovery, load balancing, and orchestration, is better suited for managing the interdependencies and communication between multiple microservices.
- **Control over deployment environment:** These services offer limited control over the underlying infrastructure and deployment environment. Kubernetes provides fine-grained control over resource allocation, scaling, and networking, allowing for optimisation and customisation based on the specific requirements of the application.
- **Integration with technologies and services:** While these services offer integrations with a certain set of technologies and services, they may not be as flexible and extensible as Kubernetes, which can integrate with a wide range of tools and platforms through its extensive ecosystem of plugins and APIs.

## User model

**All individual users on the platform are registered as Players.** These individuals may then be **assigned roles** or **attached to other entities** as appropriate for their access needs:

- **Lounges** (company accounts) are separate entities on the platform; the person who registers the Lounge is its **Superuser**, or account owner.
- **Lounge staff** are Players who are attached to a Lounge. A Player **can only be attached to one Lounge at any given time.**
- **Backroom staff** are Players who have been assigned Backroom roles.
- All Players are provided a **single point of authentication**, with an option to access the Player-facing (default), Lounge-facing, or Backroom-facing microservices.

## Platform currency

To enhance the user experience for Players, the platform will include the implementation of a **loyalty program** and a **cash deposit system** as outlined below.

### Koloseum Credits

Users will be able to top up cash on the platform as **Koloseum Credits**, which they will be able to exchange for goods and services. KXP bonuses were previously proposed for Credits top-ups, but this has been scrapped to avoid the risk of abuse; if a user can afford to top up enough Credits to earn enough KXP to win an item, they can afford to pay for the item with cash instead (especially if the item is offered by a third party).

Players will be able to use their Credits to pay for various offerings across the platform, including competition registrations, event tickets, and merchandise. Lounges will be able to use their Credits to pay for services on a rolling basis (e.g. branches, stations, add-ons, regulatory fees, etc).

Once topped up, Credits are **locked in the platform**; they cannot be withdrawn. This approach intends to build up healthy cash reserves for the platform in the long run. However, it will be imperative to notify users of this policy during the top-up process. As a safeguard against abuse (e.g. money laundering), **each Credits top-up is capped at a maximum of Ksh 10,000**, with a **maximum daily limit of Ksh 30,000**; for Lounges, these limits are raised to Ksh 30,000 and Ksh 100,000 respectively.

In the event of insufficient funds during a payment, the platform will automatically attempt to charge the user's default payment method (e.g. M-Pesa, bank card, etc) to cover the shortfall. If the payment fails, the transaction will be marked as failed and the user will be notified; if the user has not set a default payment method, they will be asked to do so for the transaction to proceed.

### Koloseum Experience Points

To encourage users to join and continue using the platform, its loyalty program will revolve around **Koloseum Experience Points (KXP)**.

All Players will automatically receive a **guaranteed minimum amount of KXP for logging into the platform at least once each day.** Certain operations on the platform will guarantee a minimum amount of KXP, with **bonuses available for specific accomplishments or milestones**; these are outlined in detail in each microservice's section. **Lounges will not be able to earn or use KXP.**

As an added incentive, we plan to eventually allow all users to trade KXP for tickets and merchandise, as well as external rewards (e.g. food deals, gym memberships, etc). To ensure KXP remain reasonably less valuable than cash, **the platform will assume an exchange rate of 20 KXP for every Kenyan shilling.** This makes it easier to value KXP transactions with a view to setting up the program for external rewards; for example, a one-month gym membership priced at Ksh 3,500 can be assumed to be worth 70,000 KXP.

Under this arrangement, it is therefore implied that **to earn enough KXP to win an item, a user requires twenty times the effort it would take to earn enough cash to purchase the same item.**

### Prize money balance

Players will have a **prize money balance** consisting of **funds accumulated from competition winnings.** They will be able to convert (some of) the balance to Credits, or make a withdrawal through one of their saved payment methods; the minimum withdrawal amount is Ksh 500 or the current balance, whichever is lower.

### Additional context

The platform previously had a proposed currency called **Koloseum Game Tokens (KGT)**, which users would buy to spend on engaging in certain activities on the platform, such as registering for tournaments.

The idea behind KGT was a **single currency to access all platform competitions**. However, this would **assume that all competitions on the platform are priced the same**, which is not true as pricing each involves considering its own unique set of factors.

This means that different variations (or denominations, if you will) of KGT would then have to be sold at different prices, rendering them **structurally identical to any other store item.** Therefore, the KGT proposal was dropped in favour of selling competition registrations as tickets, paid for with Koloseum Credits.

## Sessions / Operations

The platform previously proposed allowing Lounges to charge for sessions by a custom unit (e.g. per game, match, song, etc), but this has been scrapped in favour of charging by unit of time only. Charging by time is believed to be the most efficient way to give the Player more session time while also maximising revenue for the Lounge.

Lounges are **able to offer multiple time-based pricing options** for the Player to choose from; no per-game, per-match, per-song, or other non-time unit pricing. The minimum billing unit for a session is **15 minutes**, and the maximum is **one day (24 hours)** or a **timeframe equal to the branch's opening hours**, whichever is shorter (e.g. if the branch is open from 9am to 9pm, the maximum billing unit is 12 hours).

Lounges will also be able to **offer discounts targeting regulars or non-regulars**, as well as **specific genres or even specific games.** For a discount to apply, the Player must belong to the targeted group and/or have played the targeted genres/game(s) for at least one hour during that session; if the Player qualifies for multiple discounts, only the highest available discount will be applied.

### Regulars

Lounges will be able to **monitor their regulars**, determined by the platform as Players who have **had at least one session a week** (each lasting for at least one hour) at any of the Lounge's branches **for at least one full month.** Regulars will enjoy the following benefits at the Lounge:

- **Able to reserve stations at any branch** (for up to 10 minutes at a time; only one reservation request per hour; only one reservation per day);
- **Save the Lounge 50% on the standard hourly rate charged to them** for each session (gives the Lounge an incentive to increase and retain regulars for maximum revenue);
- **Have their regular status automatically renewed on a monthly basis** if they continue to meet the criteria, with KXP bonuses available for milestones (e.g. 3 months, 6 months, 1 year, 2 years, etc).

Regulars will have their status **automatically revoked if they fail to meet the criteria for 3 consecutive months.** If the Player qualifies as a regular at a different Lounge during this period, they will have the option to immediately relinquish their regular status at the current Lounge and become a regular at the new one (so they need not wait for automatic loss at the old Lounge).

### Revenue model

The platform will primarily generate revenue from charging Lounges for managing their business using our Operations microservice, with payments made in Koloseum Credits. The following are the revenue streams and proposed pricing:

- **Branches:** 1 branch free for all Lounges. Additional branches: Ksh 5,000 per month; Ksh 13,000 per quarter (saving Ksh 2,000); Ksh 50,000 per year (saving Ksh 10,000).
- **Stations:** 1 station free per branch; additional stations charged per hour used in Sessions. Charges include:
    - Standard rate, equivalent to Ksh 20/hour (50% discount for Lounge regulars, equivalent to Ksh 10/hour);
    - Prize Fund, equivalent to Ksh 10/hour;
    - Special controllers (e.g. racing wheel, guitar, etc), equivalent to Ksh 5/hour (40% of this charge may go to the Prize Fund).
- **Eatery add-on:** Additional Ksh 1,000 per month; Ksh 2,500 per quarter (saving Ksh 2,000); Ksh 8,000 per year (saving Ksh 4,000).

All services are debited from the Lounge's Credits balance either on a rolling basis (by default) or in one-time payments.

### Opening and closing branches

Each branch has set opening times (configurable; e.g. weekdays from 9am to 9pm). These define the window within which the branch may be marked open or closed. As such, **Lounge staff may only mark a branch as open within its set opening times.** If a branch is set to open from 9am to 9pm on weekdays, staff cannot mark it open on Saturday at noon or on Monday at 7am; this restriction exists to build trust with customers by being predictable with branch opening times.

The **platform does not automatically mark a branch as open or closed** based on the current time. For example, if the current time is 10am on Tuesday and the branch is set to open from 9am to 9pm on weekdays, the branch is not auto-opened; it becomes open only when staff open it. The same applies to closing; only when staff close the branch does it become closed.

In future updates, the platform could allow staff with the requisite roles to authenticate themselves (e.g. OTP, scanning a badge) in another staff member's account (e.g. one who has the Front Desk role) to perform the open/close operation and save time.

### Incremental time discounts

Lounges can set an **incremental discount rate on a per-hour price** (not on prices for other billing units, e.g. per 15 minutes), applying from the second hour onwards and capped at a maximum discount rate also set by the Lounge (50% by default). The `final session cost` quoted to the Player when an incremental time discount applies is calculated as follows:

```text
base session cost = hours spent × price per hour
cumulative discount rate = incremental discount rate × (hours spent - 1)
final discount rate = min(cumulative discount rate, maximum discount rate)
final session cost = base session cost - (base session cost × final discount rate)
```

Consider the following examples.

**Example 1:** Player A spends 4 hours in a session with a price per hour of Ksh 200, an incremental discount rate of 5% per additional hour, and a maximum discount rate of 50%. Therefore, the `base session cost` is Ksh 800 and the `final discount rate` is 15%, resulting in a `final session cost` of Ksh 680:

```text
base session cost = 4 hours × Ksh 200 = Ksh 800
cumulative discount rate = 5% × (4 hours - 1) = 15%
final discount rate = min(15%, 50%) = 15%
final session cost = Ksh 800 - (Ksh 800 × 15%) = Ksh 800 - Ksh 120 = Ksh 680
```

**Example 2:** Player B spends 10 hours in a session with a price per hour of Ksh 200, an incremental discount rate of 10% per additional hour, and a maximum discount rate of 40%. Therefore, the `base session cost` is Ksh 2,000 and the `final discount rate` is 40%, resulting in a `final session cost` of Ksh 1,200:

```text
base session cost = 10 hours × Ksh 200 = Ksh 2,000
cumulative discount rate = 10% × (10 hours - 1) = 90%
final discount rate = min(90%, 40%) = 40%
final session cost = Ksh 2,000 - (Ksh 2,000 × 40%) = Ksh 2,000 - Ksh 800 = Ksh 1,200
```

Like any other discount, an incremental time discount can be targeted at (non-)regulars, specific genres, or specific games.

### Special events

The platform will also allow Lounges to run **special events** at their branches with **adjusted opening times** and **limited-time pricing**. For example, to celebrate the final day of the Tekken World Tour 2025 Finals (1 February 2026), a branch that normally opens from 9am to 9pm may open that day for **24 hours** (9am to 9am) with the following offers:

- One-time **full-day price** covering the full 24 hours;
- **5% off on all sessions that include any fighting game**;
- **10% off on all sessions that include any _Tekken_ game**;
- **15% off on all sessions opened by regulars that include _Tekken 8_**.

## Markets (Competitions)

Previously, we intended to deploy our competitions on the platform through curated **Markets**, referring to a suite of services for each of the esports brands on our portfolio:

- **Ma Esto** (football)
- **Savanna FGC** (fighting games)
- **Keel Kash** (battle royale)

This approach was intended to make it easier to provide tailor-made services for each Market's community, allowing the user access only to the competitions they're interested in most. It was also intended to streamline resource management, as more resources could be allocated to a Market experiencing higher traffic, and away from a Market with lower demand.

However, we later realised that it would be much more efficient to develop a single **Competitions** microservice instead, with both participation and administration deployed on the Player-facing side. This approach brings the following benefits:

- The workload is streamlined as developing an additional Competitions microservice for Backroom staff, just to administer a handful of competitions, is no longer necessary.
- Our tournament administration applications are made available to the public, introducing a new vehicle for sustained traffic and potentially opening up additional opportunities for revenue generation.
- The initial benefits enjoyed from the original Markets arrangement are maintained; individual organisers can still monitor their own competitions, all under one roof as opposed to multiple microservices. Under this new system, each Market can exist on the platform as an individual "competition organiser".

The Competitions microservice is free to use, with users charged for advanced features that may interact with external providers and incur additional costs, such as SMS notifications to Players participating in a tournament (e.g. check-in time, time and station for their next set, etc).

# Public

The following section will discuss all microservices available to the general public:

- Landing
- Authentication
- Help
- Legal

## Landing

**Microservice tag:** `public-landing`

**Summary:** The platform's landing section that users see when they first visit. It presents marketing content for Sessions, Competitions, and KLSM (store, game exchange, and events). This microservice will be developed once all the other MVP microservices are complete, so that platform functionality can be accurately covered in its entirety.

**Business objectives**

- Attract prospective users by communicating the platform's value.
- Present primary MVP offerings with dedicated marketing content.

### Features

- Users can view a home area that highlights primary MVP offerings with unique selling points and links to pages for each:
    - **Sessions**, **Competitions**, and **KLSM** (Players);
    - **Operations** and **Staff** (Lounges).
- Players-focused content to showcase a variety of options for users, whether casual or competitive.
- Lounges-focused content to showcase the business management convenience of the platform.
- Users can access and browse other microservices from the landing experience as needed.

## Authentication

**Microservice tag:** `public-auth`

**Summary:** The platform's sole authentication microservice, allowing users to sign in or create an account. Individuals register as **Players**; companies register as **Lounges**. Lounge staff are existing Players who are later attached to a Lounge and assigned appropriate roles.

**Business objectives**

- Secure, verified, and simplified access to the platform.
- Compliance with age and identity verification requirements.
- Support for both Player and Lounge authentication with appropriate data collection.

### Features

#### Login

- Users log in using their phone number and a one-time passcode (via SMS) or password, with two-factor authentication (e.g. authentication app) also enforced if enabled by the user. The login form provides an option to access the **Player** (default) or **Lounge** microservices; there is no separate Lounge authentication flow.
- **Password reset:** Users can request a password reset via email (once set after registration) and complete the reset using the provided link.

#### Registration

- New users register using their phone number and a one-time passcode, then choose to register as a **Player** (individual) or **Lounge** (company).
- Player registration collects: ID verification (national ID, alien ID or passport, Kenya only); parent/guardian details for minors (ID, phone verification, personal information, relation); personal information (names, date of birth, nationality, pseudonym, sex, optional gender identity and pronouns, optional varsity details); gaming and socials (primary platform, online ID, console ownership, social media from predefined lists). Users can optionally hide date of birth or show pronouns on profiles.
- Lounge registration collects: Company verification (business registration number, Kenya only); company information (name, registration date, trade name, website); Superuser information (ID verification, personal information; Superuser selectable from directors retrieved at company verification); branch information (name, full address, amenities with optional evidence for CCTV and fire exit, contacts).
- After registration, users set their password and are redirected; **Players** to **`players-sessions`**, and **Lounges** to **`lounges-operations`**. Default landing microservice for each path can be changed later in **`players-account`**.

### Koloseum Experience Points

- All users receive a guaranteed minimum KXP amount upon completing registration or logging in for the first time that day.

### Rules and constraints

- Age gates: Players must be at least 13 years old; Lounge account owners must be at least 18 years old. Phone as primary identifier; 2FA at login. Consoles and social platforms from predefined lists (e.g. IGDB). Lounges claiming CCTV or fire exit require verified documentary evidence before the flag is removed for Players.
- Personal information (including sex and gender) cannot be updated again for one year after registration or after a prior update. Pseudonym may be updated once after registration, then once per year after each update. Exceptions may be made on a case-by-case basis.
- Sex and gender identity are not displayed on profiles and are collected primarily for analytics; use of this data must be covered in the platform's Privacy Policy and Code of Conduct.

## Help

**Microservice tag:** `public-help`

**Summary:** The platform's knowledge base, providing help articles (including getting-started guides) and tracking of support tickets for authenticated users. This microservice will be developed once all the other MVP microservices are complete, so that platform functionality can be accurately covered in its entirety.

**Business objectives**

- Self-service via the knowledge base to reduce repeat questions.
- Transparency on ticket status for users who have contacted support.
- Single place for help content and ticket tracking.

### Features

#### Knowledge base

- Users can browse and read knowledge base articles (from Zoho Desk), including basic guides for different areas of the platform (e.g. Sessions, Competitions, KLSM, etc).

#### Support tickets

- Authenticated users can view a list of their support tickets (identified by Player ID in the subject, with contact email as fallback), with status and optional link to the full ticket; no requirement to display individual messages in-app.

### Rules and constraints

- Support requests are created by email to [support@koloseum.ke](mailto:support@koloseum.ke) with subject format `[KPXXXXXXX] -- RE: [Title]` ("KPXXXXXXX" represents the user's Player ID). Ticket list is available only when the user is signed in. Content and categories are driven by Zoho Desk (portal/org configuration). Implementation incorporates the Zoho Desk API and server-side OAuth.

## Legal

**Microservice tag:** `public-legal`

**Summary:** A static site that allows users to view the platform's **Terms of Use**, **Privacy Policy**, and **Code of Conduct**. This microservice will be developed once all the other MVP microservices are complete, so that platform functionality can be accurately covered in its entirety.

**Business objectives**

- Transparency and compliance with legal and community standards.
- Single place for policy documentation and conduct information.

### Features

- Users can view dedicated pages for each of the platform's policy documents.
- Acceptance of policy documents to be tracked in user data, including when documents are updated.

# Players

The following section will discuss all microservices available to **Players**, including those with Backroom roles or attached to a Lounge as staff:

- Sessions
- KLSM
- Competitions
- Account

These microservices will be accessible to the general public, with unauthenticated users redirected to `public-auth` for operations requiring an account (e.g. joining a competition, tracking a store order, etc).

## General functionality

Users will have a shared shell across Player microservices: a microservices menu, a quick check-in entry point for Sessions (while the user is a Lounge regular), and buttons for notifications, `players-account`, `public-help`, `public-legal`, and logout. Unauthenticated users can access `public-auth` from this shell with a login button.

## Sessions

**Microservice tag:** `players-sessions`

**Summary:** Allows users to check in at any Lounge branch and open an active session for casual play or scheduled activities (e.g. competitive fixtures). Users can also check out of a session and pay for the time used.

**Business objectives**

- Enable Lounges to increase traffic to their branches by providing convenience in discovery for Players.
- Provide Players with access to various Lounge services (e.g. station reservation, competitive fixtures, etc) at their fingertips.
- Allow Players to give feedback on their experience at Lounge branches.

### Features

#### Home

- When the user has no active session: entry point to Start Session and Find a Lounge (and, from General functionality, quick check-in when the user is a Lounge regular).
- When the user has an active session: view or manage active session (or link to it).

#### Start Session

- Users can search for a Lounge by name or ID and start a session there. **Players may only check in to a branch that is currently open** (i.e. as indicated by staff); if the branch is closed, check-in and reservation are not available. If the branch has no CCTV or fire exit (or claims are unverified), the user is politely warned before proceeding.
- Users must select a game (from the Lounge's inventory) and a station (matching the game's equipment). Users select from the Lounge's time-based pricing options. Users can check in immediately or (for regulars) make a reservation. If the Lounge has luggage deposit, the user can indicate whether they have luggage to deposit; the Lounge assigns a slot on confirmation.
- Check-in requests stay pending until the Lounge confirms; then the session becomes active. Users can cancel a pending request; if they report issues, they can file a report to Koloseum staff. After Lounge acceptance, the user cannot cancel and the station is marked as occupied.
- During an active session, users can view elapsed time and current cost, station, game, and (if applicable) luggage deposit slot. Users can request a station/game change (session status becomes "Change Requested" until Lounge approves), view members, report competitive results, browse eatery (if offered), request assistance, file a complaint (against another session or the Lounge; Lounge is notified of the former only), and check out.
- On checkout, the Lounge receives a check-out request; session status becomes "Checking out" and the user sees total time and final session cost (after any applicable discounts). Once the Lounge confirms, the session ends. A 10-minute grace period applies: if the Lounge confirms within that period, the user is charged to the time of their checkout request; otherwise to the Lounge's confirmation time. Upon checkout, the station is marked as available.
- Invited users see their own elapsed time, can leave the session (no checkout), and can report competitive results; they do not see invite controls or total cost.

#### Find a Lounge

- Users can browse Lounges by search or by directory (county to Lounges) and view Lounge profile (company name, trade name, website, contacts).
- Users can view branches (ID, name, current status, address, contacts, socials, consoles, games, luggage deposit, opening times). Users can view stations (filter/sort by console, number, or price) and see station details (number, name, console, capacity, current status, online play, special controllers, pricing). The open/closed status of a branch (e.g. as displayed in Find a Lounge) reflects the current status—whether staff have marked the branch open—not the branch's set opening times.
- Users can start a session or report a Lounge or branch from a profile.

### Koloseum Credits

- Users will not spend Koloseum Credits to check out of a Lounge (end an active session); the platform will display the branch's payment information as set by the Lounge to facilitate a direct transaction.

### Koloseum Experience Points

- Guaranteed minimum KXP for checking in and checking out of a Lounge branch. Bonus KXP for each full hour spent in an active session.

### Rules and constraints

- Reservation: once per day, one request per hour, holds for up to 10 minutes; Lounge regulars only.
- Check-in and reservation are permitted only at Lounge branches that are currently open, not merely within their set opening times.
- Branches without verified CCTV or fire exit are flagged to users at check-in and reservation; the user is politely warned before proceeding.
- Grace period for checkout billing as specified above.

## KLSM

**Microservice tag:** `players-commerce`

**Summary:** Allows users to trade used games and consoles with other users, including both Players and Lounges. Planned to become a single point of commerce for merchandise and tickets in future updates.

**Business objectives**

- Enable Lounge owners and branch managers to offload old games and consoles that their customers may no longer be interested in.
- Enable Players to buy games and consoles at lower prices with quality assured as advertised.

### Features

#### Game Exchange

- **Listing (seller):** An interested seller creates a listing for a used game or console. They pay a fee (using Koloseum Credits) to make the listing; the fee is charged only once the item is confirmed as genuine by Backroom staff. The seller earns KXP for making an approved listing.
- **Browsing and reservation (buyer):** An interested buyer browses listings from the KLSM home page, finds seller's listing, and requests a reservation of the item; this allows the buyer to view the seller's contact information. The seller sees the reservation request, agrees on a meeting place and date/time with the buyer (e.g. on a phone call), then approves the reservation request. The buyer can cancel the reservation request at any time prior to the seller's approval.
- **In-person exchange:** Buyer and seller meet; the buyer inspects the item. Payment is made off-platform (as with Sessions). The seller marks the purchase as complete once they receive payment. The buyer later confirms the purchase as complete on their end, with the option to rate the seller and add a brief review.

#### Future additions

- **Store:** Users will be able to browse and buy merchandise offered by Koloseum.
- **Events:** Users will be able to browse, view details on, and buy tickets for Competitions events, including competition registrations and spectator options.

### Koloseum Credits

- **Game Exchange:** Seller pays a fee to make a listing, charged only once Backroom staff have confirmed the item as genuine.

### Koloseum Experience Points

- **Game Exchange:** Seller earns KXP for making a listing (once confirmed) and for successfully selling a listed item (after buyer confirmation). Buyer earns KXP for reserving a listed item (after seller approval) and for purchasing a listed item (after buyer confirmation).

## Competitions

**Microservice tag:** `players-competitions`

**Summary:** Allows Players to discover and participate in Leagues, Challenges, and Locals, register as competition organisers to administer their own competitions, and donate to prize pools. Players may also view competition results, rankings, and statistics as appropriate.

**Business objectives**

- Give Players easy access to structured competitions administered by community organisers.
- Facilitate content creation with easy access to competition results, rankings, and statistics.
- Give community organisers access to additional prize pool funding from community donations to incentivise participation.

### Features

#### Home

- High-level discovery and entry to Leagues, Challenges, and Locals, as well as organiser registration.
- Quick access to any upcoming competitions for the user (i.e. League tournaments, Challenges editions, or Locals tournaments).

#### Shared functionality (Leagues, Challenges, Locals)

- Users have three competition classes to interact with on the platform: Leagues (tiers, divisions, seasons, tournaments), Challenges (tiers, divisions, editions), and Locals (single tournaments).
- Users can discover and participate in or purchase tickets for competitions, track their progress at a live event, and review past results, rankings, and statistics.
- Organisers can create and configure competitions (Leagues, Challenges, Locals), manage staff, set rules and formats, configure rankings, sell tickets on KLSM (i.e. competition registrations and spectator options), and administer live events (including check-in with OTP and match reporting).
- Organisers may run a League tournament, Challenges edition, or Locals tournament at a Lounge branch (with approval from Lounge staff), at a custom address, or online.
- Users can donate to the prize pool of a League tournament, Challenges edition, or Locals tournament. The organiser sets a base total, allows donations, and sets a hard cap; when the total reaches the cap, further donations are automatically rejected.

#### Organisers

- Users can register as a competition organiser, update their organiser profile, and manage staff.

### Koloseum Experience Points

- Guaranteed minimum KXP for participating in (rather than just joining) a League tournament, Challenges edition, or Locals tournament; bonus KXP for winning the competition (with multipliers for higher tiers and divisions where applicable).

### Rules and constraints

- Profile display rules for DOB and pronouns (as set by the Player) are enforced.
- Organisers may only manage competitions they have created or been added to as staff.
- Any prize money advertised by an organiser (without donations) must be deposited into the platform before the competition can begin. The platform then distributes funds to the winners' prize money balances after the competition ends.
- For prize pool donations, configuration (i.e. base pool, optional cap) is set by the organiser and stored in the `competitions` schema, with contributions recorded in the `commerce` schema.

## Account

**Microservice tag:** `players-account`

**Summary:** Allows users to review and manage their account information, settings, and balances, with additional views for users with Lounge and Backroom roles.

**Business objectives**

- Provide a central and convenient place for Players to manage their accounts.
- Provide Lounge and Backroom staff with transparency over their access.

### Features

#### General

- Users can review and manage general contacts (i.e. phone, email).
- Users can review their Lounge regular status (i.e. Lounge name, duration, last renewal date).
- Users can set their default landing for Player login (i.e. `players-sessions` by default) and Lounge login (i.e. `lounges-operations` by default); only Lounge staff can view and change their Lounge default, and the available options are the microservices they have access to given their role (e.g. Operations, Staff, etc. — only those they are authorised to use).

#### Security

- Users can change their password, enable two-factor authentication, and link their account to third-party providers.

#### Finances

- Users can review their current Credits balance and transaction history.
- Users can top up their Credits (minimum amount is Ksh 50).
- Users can monitor their current prize money balance (from Competitions), make withdrawals, or convert (some of) the balance to Credits.

#### Koloseum Experience Points

- Users can review their current KXP balance and transaction history.

#### Notifications

- Users can enable or disable notifications and manage preferences (e.g. SMS, email).

#### Documents

- Users can review and manage their ID documents (i.e. national ID, alien ID, or passport as collected at registration).
- Documents are stored securely and not shared with any third parties.

#### Personal information

- Users can review and manage personal information as collected at registration, including profile display rules (e.g. optionally hide date of birth or show pronouns on profiles).

#### Gaming and socials

- Users can review and manage their gaming and social media information as collected at registration: primary platform, online ID, console ownership, and social media (from predefined lists).

#### Lounges and Backroom

- While the user is attached to a Lounge or has Backroom roles, they can review their current Lounge/Backroom role and a read-only view of microservices and features with their access indicated.

### Rules and constraints

These restrictions are in place to ensure consistency in the user's identity and mitigate abuse of the platform.

- Updates to identity fields (i.e. first name, middle names, last name, date of birth, nationality) trigger a data update request to be reviewed and approved by Backroom staff; users cannot change these fields directly.
- Pseudonym may only be updated once after registration and once per year thereafter.
- The minimum prize money withdrawal amount is Ksh 500 or the current balance, whichever is lower.

# Lounges

The following section will discuss all microservices available to **Lounge** account owners and their staff:

- Operations
- Staff
- Account

## General functionality

Users will have a shared shell across Lounge microservices: a microservices menu and buttons for notifications, `lounges-account` (or `players-account` if not authorised), `public-help`, `public-legal`, and logout. Unauthenticated users are automatically redirected to `public-auth`.

## Operations

**Microservice tag:** `lounges-operations`

**Access:** Superuser, Manager, Branch Manager, Front Desk, Inventory, Kitchen

**Summary:** Allows Lounge owners and staff to manage day-to-day operations: branch management, session management and history, inventory (games and stations), eatery (menu and orders), KLSM trading, and sales review.

**Business objectives**

- Provide a solution for Lounge owners and staff to streamline administrative operations.
- Use Koloseum Credits to collect revenue from Lounges with reliable, accurate, and transparent pricing.

### Features

_N.B. Staff with the **Branch Manager**, **Inventory**, **Front Desk**, and **Kitchen** roles can only review information and carry out operations for their assigned branch._

#### Dashboard

- Staff can review a high-level summary of branch activity and access all other listed features (relevant to their current role) from the dashboard.
- Staff can view a list of their Lounge's regulars (pseudonym/full name, duration, last renewal date).

#### Branches

- **Access:** Superuser, Manager, Branch Manager
- Users can add or remove branches and update branch details (including payment collection info).
- Payment collection info for a branch is displayed to Players at checkout for easy reference; it does not act as a middleman collecting a percentage of payments. This is made clear to users to build trust and allow accurate tracking of platform revenues.
- Users can track their amenity approval requests (i.e. for CCTV and fire exits) and view their history.

#### Inventory

- **Access:** Superuser, Manager, Branch Manager, Inventory
- Staff can add, delete, and update games and their consoles/quantities (with optional equipment flags).
- Staff can add, delete, and update stations (number, name, console, capacity, equipment flags, price options).
- Staff can sell and trade games and consoles from their existing inventory on the KLSM market (capability; detailed flows omitted from this high-level spec).

_N.B. Staff with the **Inventory** role cannot sell or trade games and consoles on KLSM._

#### Front Desk

- **Access:** Superuser, Manager, Branch Manager, Front Desk
- Staff can open or close the branch, view and process check-in requests (confirm to set session as active and station as occupied; assign luggage slot if applicable), station-change requests (confirm to set session back to active and switch to new station), and check-out requests (confirm to request payment, end session, and set station as available).

_N.B. Staff with the **Front Desk** role cannot open or close a branch._

#### Sessions

- **Access:** Superuser, Manager, Branch Manager, Front Desk
- Staff can manage pending and active sessions (ID, player, luggage slot, current station, current game, other members, elapsed time, cost), start pending sessions for Players (with Player verification to set session as active and station as occupied; assign luggage slot if applicable), and end active sessions (with payment confirmation; competitive fixtures auto-verified on end).
- Staff can start sessions for non-Koloseum users (collect name and phone to encourage registration after session end; select station and game; optional luggage).
- Staff can search and view session history with revenue generated (i.e. session cost, eatery orders).
- Staff can manage discounts (including incremental time discounts) and special events.

_N.B. Staff with the **Front Desk** role cannot manage discounts or special events._

#### Eatery

This feature is an optional add-on and only available to Lounges that opt in (at an additional cost).

- **Access:** Superuser, Manager, Branch Manager, Kitchen
- Staff can manage orders (filter by status: pending, acknowledged, fulfilled, cancelled; filter by user ID; view order items and cancellation reasons).
- Staff can manage menu items (food, drinks, snacks) with categories, flavours, sizes, and prices. Orders update in real time as Players and staff interact with the eatery during active sessions.

#### Reputation

- **Access:** Superuser, Manager, Branch Manager, Front Desk, Inventory, Kitchen
- Staff get a high-level view of their Lounge's reputation (average rating, number of reviews, number of misconduct reports).
- Staff can view and manage reviews (filter/sort by branch, rating, date; view details).
- Staff can review and manage sanctions (including bans) issued by their Lounge (filter/sort by branch, date; view details).
- Staff can review and manage sanctions (including bans) issued to their Lounge by Backroom staff (filter/sort by branch, date; view details).

_N.B. Staff with the **Front Desk**, **Inventory**, and **Kitchen** roles can only perform read operations in this microservice._

### Koloseum Credits

- Lounges pay for additional branches, additional stations, and add-ons with Koloseum Credits; service charges are debited either on a rolling basis or in one-time payments.

### Rules and constraints

- Role-based access as above.
- Station status (available/occupied) follows session lifecycle.
- Branch opening hours determine the maximum billing unit for sessions (see [Sessions / Operations](#sessions--operations)).
- Branch open/closed status is set by staff (within set opening times) and is not automatically updated by the platform based on the clock.

## Staff

**Microservice tag:** `lounges-staff`

**Summary:** Allows users to recruit staff by linking existing Players to the Lounge and assigning roles.

**Access:** Superuser, Human Resources.

**Business objectives**

- Provide a convenient and efficient solution for Lounges to manage their staff.
- Promote a culture of role-based access to technology in the Kenyan esports industry.

### Features

#### Dashboard

- High-level summary of existing staff statistics (e.g. total, total by role, active, inactive, etc).
- Access to Roles and Users (with search and filter capabilities as appropriate).

#### Roles

- Users who register a Lounge account are assigned the **Superuser** role, giving them unlimited access.
- Users can review the default roles assigned by the platform, each with limited access to microservices and features: **Manager**, **Branch Manager**, **Front Desk**, **Inventory**, **Kitchen**, and **Human Resources**.
- In future updates, users may be able to create custom roles by selecting which Lounge microservices and features each role can access.

#### Users

- Users can search and view staff list (name, branch, role) and view profile for each (Player ID, name, DOB, age, pseudonym, pronouns, roles, recruitment date). Users can edit roles (with disclaimer) and dismiss staff (with disclaimer) from their profiles.
- Users can add staff by linking existing Players to the Lounge and assigning roles (Player receives a notification to accept the invitation). If the Branch Manager, Front Desk, Inventory, or Kitchen role is assigned, the Player must be assigned to a specific branch.

### Rules and constraints

- Superuser role is permanent for the account creator and can never be removed; it can only be transferred to another user at the owner's request.
- Role and deletion actions must include acceptance of liability disclaimer.
- A Player can only be attached to one Lounge at any given time; only those currently not linked to any Lounge may be recruited as staff. This is enforced so that `players-account` can offer a single default landing choice scoped to that Lounge and the user's role.

## Account

**Microservice tag:** `lounges-account`

**Summary:** Allows Lounge owners and staff to manage their company information, platform finances, and account ownership.

**Access:** Superuser, Manager.

**Business objectives**

- Provide a central and convenient place for Lounge owners and staff to manage their accounts.
- Enable ownership transfer of the Lounge account at the owner's request.

### Features

#### Company information

- **Access:** Superuser
- Users can review and update Lounge company information (e.g. company name, trade name).

#### Finances

- **Access:** Superuser, Manager
- Users can review their Lounge's current Credits balance and transaction history.
- Users can top up their Lounge's Credits (minimum amount is Ksh 50).
- Users can track and manage their Lounge's current service subscriptions (e.g. branches, stations, add-ons, etc).

#### Ownership transfer

- **Access:** Superuser
- Users can request a transfer of Lounge account ownership to another user (by Player ID; request may include supporting documents). The request is later reviewed and approved or rejected by Backroom staff at their discretion.

### Rules and constraints

These restrictions are in place to ensure consistency in the Lounge's identity and mitigate abuse of the platform.

- Updates to company-level identity fields (i.e. company name, trade name, registration date) and branch-level fields (i.e. address, amenities) trigger a data update request to be reviewed and approved by Backroom staff; users cannot change these fields directly.
- Trade name may only be updated once after registration and once per year thereafter.
- Manager role cannot update company information or transfer account ownership.

# Backroom

The following section will discuss all microservices available to **Koloseum staff and representatives**:

- Compliance
- KLSM
- Staff

## General functionality

Users will have a shared shell across Backroom microservices: a microservices menu and buttons for notifications, `players-account`, `public-help`, `public-legal`, and logout. Unauthenticated users are automatically redirected to `public-auth`.

## Compliance

**Microservice tag:** `backroom-compliance`

**Summary:** Allows staff to manage compliance and regulatory requirements for Players and Lounges: review registrations, track documents, approve or reject data updates and amenity evidence, grant services, review service payments and misconduct reports, and issue sanctions (including bans).

**Access:** Superuser, Compliance, Players, Lounges.

**Business objectives**

- Ensure Players and Lounges are verified and their documents are valid.
- Effectively manage any reported misconduct on the platform.
- Facilitate quick resolution of account-related enquiries and support requests.

### Features

#### Dashboard

- Staff get a high-level view of various platform statistics (e.g. total registered users, monthly active users, users online, etc).
- Staff get quick access to a list of pending data update requests and misconduct reports for both Players and Lounges.

#### Players

- **Access:** Superuser, Compliance, Players
- Staff can search Players (by ID, name, pseudonym) and view profile for each (IDs, name, DOB, age, pseudonym, pronouns, join date, varsity if provided)
- Staff can review documents (verified ID with type, number, country, issue/expiry, biometric confirmation).
- Staff can review data update requests (identity fields) and approve or reject (reason required for rejection). They can also view data update history.
- Staff can review and acknowledge misconduct reports (category, microservice, date, description, attachments, links).
- Staff can review and issue sanctions (category; status: active/expired; date; reason; optional: Lounge/competition; optional KXP deduction).

#### Lounges

- **Access:** Superuser, Compliance, Lounges
- Staff can search Lounges (by ID, phone) and view profile for each (IDs, company details, registration dates).
- Staff can review documents (i.e. company registration).
- Staff can view Lounge branches (by ID, name) and view profile for each (address, amenities, phone, email).
- Staff can review data update requests (identity fields) and amenity approval requests and approve or reject (reason required for rejection). They can also view data update and amenity approval history.
- Staff can review service payments made by the Lounge (transactions, transfers, subscriptions) and grant services (service type, duration, include/exclude list of features).
- Staff can review and acknowledge misconduct reports (category, microservice, date, description, attachments, links).
- Staff can review and issue sanctions (category; status: active/expired; date; reason; optional: branch/competition).

### Koloseum Credits

- Staff grant services (subscriptions) to Lounges; payment must be completed on the Lounge's end (by Superuser or Manager) to confirm the granted service.

### Koloseum Experience Points

- Sanctions against Players may include a KXP fine (i.e. deduction from their balance).

### Rules and constraints

- Misconduct reports use the following categories: Nuisance, Discrimination, Harassment, Criminal Activity.
- Sanctions may restrict a Player from a particular Lounge (or branch), competition, or the entire platform. KXP may also be deducted from their balance as a fine.
- Sanctions may restrict a Lounge from a particular competition or the entire platform. They may also target a particular branch, if not the entire Lounge.
- Sanctions issued by Backroom staff on behalf of a Lounge must be confirmed on the Lounge side by a Superuser or Manager.
- Data update requests: Player identity fields (first name, middle names, last name, date of birth, nationality); Lounge identity fields (company name, trade name, registration date); branch-level fields (address, amenities).
- Misconduct report description: 1,500 characters; attached files: 30MB total; external evidence links: 5; sanction reason: 500 characters; amenity rejection reason: 500 characters.

### Future work

In future updates, Players and Lounges will have the option of paying a fee for staff to monitor their regulatory compliance with relevant authorities (e.g. Sports Registrar, esports federations and associations, etc). This option will be gently recommended to users for its convenience in navigating the bureaucracy often associated with such requirements.

## KLSM

**Microservice tag:** `backroom-commerce`

**Summary:** Allows staff to manage store inventory, track payments and orders, review and manage KLSM listings, and monitor revenue generation.

**Access:** Superuser, Commerce, Storefront, Warehouse, Game Exchange.

**Business objectives**

- Facilitate the trading of used games and consoles between Players and Lounges.
- Prepare to sell merchandise in future updates.

### Features

#### Dashboard

- Staff get a high-level view of revenue generated on KLSM.
- Staff get quick access to Game Exchange.

#### Game Exchange

- **Access:** Superuser, Commerce, Game Exchange
- Staff can review listings (filter/sort by status, date, seller) and confirm listed items as genuine.
- Staff can review listing statistics (e.g. number of listings, filter/sort by game/console, etc) and track revenue generated from listing fees.

#### Future additions

- **Store:** Manage inventory and discounts, track payments and orders.
- **Events:** Review (and revoke, if necessary) tickets created by competition organisers and track attendance (number of users checked in as competitors or spectators).

## Staff

**Microservice tag:** `backroom-staff`

**Summary:** Allows staff to assign and manage Backroom roles for Players.

**Access:** Superuser, Human Resources.

**Business objectives**

- Effectively manage Backroom access on the platform.
- Support future expansion into a full HR management tool.

### Features

#### Dashboard

- Staff get a high-level view of Backroom staff and roles.

#### Roles

- Staff can review the default roles assigned by the platform, each with limited access to microservices and features: **Compliance** (Players and Lounges), **Media** (for _The Focus_), **Commerce** (including Storefront and Warehouse), and **Human Resources**.
- Staff can assign roles to existing Players (by ID or phone number; Player receives a notification to accept the invitation).

#### Users

- Users can search and view staff list (name, department, role) and view profile for each (Player ID, name, DOB, age, pseudonym, pronouns, roles, recruitment date). Users can edit and revoke roles (with disclaimer) from profiles.
- Users can add staff by assigning Backroom roles to existing Players, who will receive a notification to accept the invitation.

### Rules and constraints

- The **Superuser** role is permanent and restricted to the three founding directors of Ace Pro Sports Technologies: Fanuel Opiyo, Davy Kamanzi, and Daniel Juma.

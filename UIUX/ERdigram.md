UI / UX ER Diagram – Online Banking (iNB)

Purpose:
To model UI screens and interactions, not tables.
This helps UX designers, frontend developers, and architects align on:


What screens exist
How they are connected
What data they consume
What actions users can perform


1. UX ERD Concepts (Entities)
For UI/UX, we model four core entities:

Entity        Meaning
UserRole      Who is using the system
Screen        A UI page or view
UIComponent   Reusable UI elements
Action        User interactions (clicks, submits, etc.)
State        UI state after an action

2. UX ER Diagram – Entity Definitions
2.1 UserRole
Represents role‑based UI access.
UserRole

RoleId (PK)
RoleName (Customer / Banker / Admin)
Description


2.2 Screen
Represents each UI page.
Screen

ScreenId (PK)
ScreenName
ScreenType (Public / Secure / Admin)
Description
IsPostLogin (Yes/No)


2.3 UIComponent
Reusable elements shown on screens.
UIComponent

ComponentId (PK)
ComponentName
ComponentType
(Header, Footer, Navigation, Form, Table, Card, Modal, Button)


2.4 Action
Represents a user interaction.
Action

ActionId (PK)
ActionName
ActionType
(Navigation / CRUD / Submit / Approve / Reject / Search)


2.5 UIState
Represents screen states after actions.
UIState

StateId (PK)
StateName
(Loaded, Success, Error, Locked, Pending, Empty)
MessageShown


3. UX Relationships (Core of the Diagram)
These are the relationships you will draw between entities in draw.io.

3.1 UserRole → Screen
UserRole 1 —— N Screen

Meaning:

Each role can access many screens
Screens are role-restricted

Examples:

Customer → Home, Accounts, Payments
Banker → Approvals, Cheque Processing
Admin → Parameters, User Unlock


3.2 Screen → UIComponent
Screen 1 —— N UIComponent

Meaning:

Each screen contains multiple UI components
Components are reused across screens

Examples:

Header, Footer, Left Navigation appear on all secure screens
Forms and tables are screen-specific


3.3 Screen → Action
Screen 1 —— N Action

Meaning:

A screen exposes multiple user actions

Examples:

Login Screen → Login Action
Payment Screen → Pay Now, Schedule Payment


3.4 Action → Screen (Navigation)
Action N —— 1 Screen

Meaning:

An action navigates to or refreshes another screen

Examples:

Login → Home Screen
Approve Registration → Registration List Screen


3.5 Action → UIState
Action 1 —— N UIState

Meaning:

Each action results in one or more UI states

Examples:

Login → Success / Error / Locked
Cheque Status Update → Updated / Failed


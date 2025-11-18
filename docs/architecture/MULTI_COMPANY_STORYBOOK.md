# \# MULTI\_COMPANY\_STORYBOOK

# 

# The Multi-Company Storybook defines \*\*scenario-driven examples\*\* that help developers, designers, and Cursor AI understand how CephasOps behaves across different companies, brands, sites, and business verticals.

# 

# Each scenario demonstrates:

# \- Company context switching  

# \- Branding differences  

# \- Different workflows per company type  

# \- How UI components adapt to each business model  

# \- Expected behaviour for Orders, Billing, Inventory, Scheduler \& Reporting  

# 

# These scenarios serve as \*\*living documentation\*\* to guide future development and consistent UX behaviour.

# 

# ---

# 

# \# 1. UI COMPONENTS IN MULTI-COMPANY CONTEXT

# 

# \## 1.1 Company Switcher

# Displayed in the top-right navigation bar.

# 

# Shows:

# \- Logo  

# \- Company name  

# \- Short code (CEPHAS, KINGSMAN, MENORAH)  

# \- Active context

# 

# Actions:

# \- Dropdown to switch companies  

# \- “Group Mode” for users with multi-company permissions  

# \- Badge indicator when company is recently switched  

# 

# ---

# 

# \## 1.2 Theme \& Branding

# Each company carries different brand elements:

# 

# | Company | Colors | Logo | UI Theme |

# |--------|--------|------|----------|

# | Cephas | Blue | Cephas Logo | Professional, engineering |

# | Kingsman | Gold / Black | Kingsman Crown | Retail, POS-like |

# | Menorah Travel | Teal | Menorah Icon | Travel booking friendly |

# 

# Frontend automatically loads:

# \- Theme tokens

# \- Logo assets

# \- Button style variations

# \- Notification rules (email/WhatsApp templates)

# 

# ---

# 

# \## 1.3 Company Context Banner

# Displayed on key operational modules:

# 

# \- Orders  

# \- Scheduler  

# \- Inventory  

# \- Billing  

# \- Reports  

# 

# Message example:

# 

# > "You are viewing: \*\*Cephas Sdn. Bhd. (CEPHAS)\*\* – ISP Operations"

# 

# ---

# 

# \## 1.4 Company Badges

# Inline badges for:

# 

# \- Orders  

# \- Installers  

# \- Jobs  

# \- Invoices  

# \- Reports  

# \- Inventory sites  

# 

# Variations:

# \- Inline pill

# \- Full badge (logo + name)

# \- Grey-out for inactive companies

# 

# ---

# 

# \# 2. SCENARIO STORIES (CORE OF THIS DOCUMENT)

# 

# Below are the \*\*full scenario-based stories\*\*, expanded from your seed reference.  

# These guide Cursor and developers when extending the platform.

# 

# ---

# 

# \# 2.1 ISP Scenario  

# \### \*TIME Activation Order — Cephas Sdn. Bhd.\*

# 

# This scenario demonstrates CephasOps when used for \*\*Internet Service Provider (ISP) operations\*\*.

# 

# \### Scenario Flow

# 1\. \*\*User logs in → Default company = Cephas Sdn. Bhd.\*\*

# 2\. Company Switcher shows:

# &nbsp;  - Logo: Cephas  

# &nbsp;  - Theme: Blue  

# &nbsp;  - Code: CEPHAS  

# 3\. User receives a \*\*TIME Activation Order\*\* via aggregator or email parser.

# 4\. Parser tags:

# &nbsp;  - Company = Cephas  

# &nbsp;  - Job Type = Activation  

# &nbsp;  - Required documents = customer ID + router  

# 5\. Scheduler view:

# &nbsp;  - Installers filtered by Cephas-approved SIs  

# &nbsp;  - Zones based on Cephas installation areas  

# 6\. Inventory:

# &nbsp;  - Router stock pulled from Cephas warehouse  

# &nbsp;  - Serial number required (Cephas setting)  

# 7\. Billing:

# &nbsp;  - Subcon SI paid based on Cephas rate card  

# 8\. Reports:

# &nbsp;  - Order completion counted toward Cephas KPI dashboard

# 

# \### Expected UI Behaviour

# \- Blue theme across UI  

# \- Cephas logo on invoice PDF (future print)  

# \- Order list filtered by `CompanyId = CEPHAS`  

# \- SI app shows Cephas branding on job card  

# 

# This scenario validates the \*\*ISP business vertical\*\*.

# 

# ---

# 

# \# 2.2 Travel Scenario  

# \### \*Booking + Invoice Flow — Menorah Travel \& Tours\*

# 

# This scenario shows how multi-company behaviour adapts to a \*\*Travel Business vertical\*\*.

# 

# \### Scenario Flow

# 1\. User switches company → \*\*Menorah Travel \& Tours\*\*

# 2\. Company Switcher theme changes:

# &nbsp;  - Teal accents  

# &nbsp;  - Menorah logo  

# 3\. New workflow tools appear (per-company features):

# &nbsp;  - Travel booking creation  

# &nbsp;  - Passenger information management  

# &nbsp;  - Package quotation builder  

# &nbsp;  - Travel invoice templates (e.g. "Tour Fee", "Deposit", “Balance Payment”)  

# 4\. Inventory is \*\*not shown\*\* (not relevant to travel).

# 5\. Scheduler repurposed for:

# &nbsp;  - Trip dates  

# &nbsp;  - Tour guide assignments  

# 6\. Billing:

# &nbsp;  - Invoice numbering uses Menorah prefix: `MTR-INV-0001`  

# &nbsp;  - Payment schedule supports multi-installment  

# 7\. Reports:

# &nbsp;  - Revenue by travel package  

# &nbsp;  - Passenger volume  

# &nbsp;  - Agent commission summary  

# 

# \### Expected UI Behaviour

# \- Teal theme  

# \- Travel-specific menu items  

# \- No router/inventory modules  

# \- Travel invoice template with Menorah header  

# 

# This scenario validates the \*\*Travel vertical\*\*.

# 

# ---

# 

# \# 2.3 Kingsman Scenario  

# \### \*Future POS / Invoice / Staff Payroll — Kingsman Classic Services\*

# 

# This scenario demonstrates how operations behave in a \*\*Retail / Barbershop vertical\*\*.

# 

# \### Scenario Flow

# 1\. User switches company → \*\*Kingsman Classic Services\*\*

# 2\. Company Switcher updates:

# &nbsp;  - Theme: Gold + Black  

# &nbsp;  - Logo: Kingsman Crown  

# 3\. Modules activated for Kingsman:

# &nbsp;  - POS / Appointment system (future)

# &nbsp;  - Barber payroll \& commission rules  

# &nbsp;  - Product inventory (hair products, vouchers)  

# &nbsp;  - Outlet scheduling  

# 4\. Inventory:

# &nbsp;  - Tracks stock per outlet (PJ, Klang, etc.)

# 5\. POS output:

# &nbsp;  - Receipt uses Kingsman styling  

# &nbsp;  - Service items (haircut, shaving, beard trim)

# 6\. Payroll:

# &nbsp;  - Per-barber commission calculation  

# &nbsp;  - Attendance tracking  

# &nbsp;  - KPI for upselling  

# 7\. Reports:

# &nbsp;  - Daily sales  

# &nbsp;  - Barber performance  

# &nbsp;  - Product movement across outlets  

# 

# \### Expected UI Behaviour

# \- Gold branding  

# \- Retail navigation structure  

# \- POS-friendly UI components  

# \- Commission / shift / outlet hierarchy  

# 

# This scenario validates the \*\*Retail vertical\*\*.

# 

# ---

# 

# \# 3. GROUP MODE (MULTI-COMPANY DASHBOARDS)

# 

# Users with “Group Ops” or “Group Finance” role can access \*\*cross-company aggregated dashboards\*\*.

# 

# \### 3.1 KPI Summary

# \- Cephas installation jobs  

# \- Kingsman sales  

# \- Menorah tour bookings  

# \- Consolidated profit margins  

# 

# \### 3.2 P\&L Cross-Company Rollup

# Grouped by:

# \- Company  

# \- Business unit  

# \- Outlet / department  

# 

# \### UI Behaviour

# \- Company Switcher shows “Group Mode”

# \- Multi-company filters enabled  

# \- Company badges visible in tables  

# 

# ---

# 

# \# 4. EDGE CASE STORIES

# 

# \### 4.1 User With No Company Assigned

# Show lock screen:

# > “You are not assigned to any company. Contact your administrator.”

# 

# \### 4.2 Company Deactivated

# If a user is inside Kingsman and Kingsman is deactivated:

# \- Immediately forced to select another company

# 

# \### 4.3 Installer App (Future)

# When receiving a job:

# \- Company branding loaded per job  

# \- SI sees only jobs belonging to their company  

# 

# ---

# 

# \# 5. FUTURE STORYBOOK EXPANSIONS

# 

# \- Kingsman POS UI component library  

# \- Menorah booking engine UI  

# \- Cephas fibre splitter locator Storybook  

# \- Multi-company invoice templates  

# \- Multi-company inventory dashboards  

# 

# ---

# 

# \# END OF DOCUMENT




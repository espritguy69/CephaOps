# MULTI_COMPANY_STORYBOOK

This Storybook demonstrates **real scenarios** showing how CephasOps behaves differently for each company and vertical based on the new unified Settings Module.

It explains how UI components, workflows, notifications, KPI behaviour, parser rules, and module visibility change **depending on active company context**.

---

# 1. UI Components in Multi-Company Context

## 1.1 Company Switcher
Visible in the global navigation bar.

Changes when switching:
- Logo
- Theme colours
- Active vertical (ISP / Retail / Travel)
- Modules shown or hidden
- Settings loaded (workflow, KPI, SI rates, billing rules)

## 1.2 Branding & Theme
Automatically loads per company:

| Company | Theme | Vertical |
|---------|--------|----------|
| Cephas | Blue | ISP Installer (CSIOS) |
| Kingsman | Gold & Black | Retail / Barbershop |
| Menorah | Teal | Travel & Tours |

## 1.3 Company Context Banner
Displayed at top of core modules:

> “Current Company: **Cephas Sdn. Bhd. (CEPHAS)** — ISP Vertical”

## 1.4 Company Badges
Used in:
- Orders  
- Jobs  
- Inventory items  
- Invoices  
- Schedules  

---

# 2. Scenario Stories (Aligned to New Settings Module)

## 2.1 ISP Scenario  
### *Cephas Sdn. Bhd. – Fibre Activation*

**Active Vertical:** ISP  
**Settings Loaded:**  
- Materials catalogue  
- Building/splitter rules  
- KPI thresholds  
- Parser rules (TBBN/TTKT)  
- SI rate matrix  
- Blocker definitions  
- Required photo types  

### Behaviour:
1. User switches to Cephas → Blue theme.  
2. Orders module loads **ISP workflow**.  
3. Parser converts incoming WO into Activation job using partner rules.  
4. Scheduler loads:
   - SI availability
   - Zone rules
   - Building/splitter hints  
5. Inventory loads:
   - Router/ONU materials  
   - Serialized numbers required  
6. Docket rules enforced:
   - ODF port photo  
   - Customer ONT photo  
7. KPI engine tracks ISP-specific SLA.

This scenario validates full ISP vertical.

---

## 2.2 Retail Scenario  
### *Kingsman – Barber Appointment + Outlet Inventory*

**Active Vertical:** Retail  
**Settings Loaded:**  
- POS service catalog  
- Commission rules  
- Product inventory  
- Appointment duration logic  
- Branding (gold & black)  

### Behaviour:
1. Company switcher → Kingsman.  
2. Theme flips to **gold/black**.  
3. Orders module replaced with **Appointments**.  
4. Scheduler adapts:
   - Barber shift schedule  
   - Service lengths  
5. Inventory:
   - Tracks shampoo, pomade, vouchers  
6. Billing:
   - Retail invoice style  
7. KPI:
   - Barber performance (upsell %, repeat clients)  

---

## 2.3 Travel Scenario  
### *Menorah – Tour Package Booking*

**Active Vertical:** Travel  
**Settings Loaded:**  
- Tour packages  
- Passenger rules  
- Deposit / balance schedule  
- Travel invoice format  
- Branding (teal)  

### Behaviour:
1. Company switcher → Menorah.  
2. Theme becomes **teal**.  
3. Orders module transforms into **Travel Bookings**.  
4. Scheduler:
   - Trip dates, assignment of tour leaders  
5. Billing:
   - Deposit → Balance → Final invoice  
6. KPI:
   - Booking volume  
   - Agent commissions  

---

# 3. Group Mode Scenario  
### *Group Ops – Multi-Company P&L*

**User Role:** Group Finance / Group Ops  
**Behaviour:**
- Company switcher shows *“Group Mode”*.  
- Reports module shows:
  - Cephas KPI + revenue
  - Kingsman daily sales
  - Menorah booking trends  
- Cross-company filtering enabled.  
- Company badges appear in each row.  

---

# 4. Edge Case Stories

## 4.1 User with No Company
Show block screen:  
> “No company assigned. Contact Admin.”

## 4.2 Deactivated Company
User is forced to change company if current one becomes inactive.

## 4.3 Installer App (Future)
Installer sees only jobs for the company assigned to them:
- Cephas jobs have blue header  
- Kingsman (future barber service jobs) have gold header  
- Menorah jobs (future travel tasks) have teal header  

---

# 5. Future Storybook Expansion

- Menorah Travel booking calendar  
- Kingsman POS receipt storybook  
- ISP ODF/port selection UI  
- Multi-company invoice templates  
- Multi-company dashboard widgets  

---

# End of Document

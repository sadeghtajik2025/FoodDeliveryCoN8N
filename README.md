<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
  <meta http-equiv="Content-Style-Type" content="text/css">
  <title></title>
  <meta name="Generator" content="Cocoa HTML Writer">
  <meta name="CocoaVersion" content="2299.77">
  <style type="text/css">
    p.p1 {margin: 0.0px 0.0px 0.0px 0.0px; font: 12.0px Times; -webkit-text-stroke: #000000}
    p.p2 {margin: 0.0px 0.0px 0.0px 0.0px; font: 12.0px Times; -webkit-text-stroke: #000000; min-height: 14.0px}
    span.s1 {font-kerning: none}
  </style>
</head>
<body>
<p class="p1"><span class="s1"># Food Delivery Automation Workflows (n8n)</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">This repository contains two complete automation workflows built in **n8n** for a food delivery platform. The workflows are designed to support core financial operations related to **courier settlements** and **customer refunds**, based strictly on internal company policy.</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">---</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">## 📦 1. WeeklySettlement Workflow</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">This workflow runs automatically **every Monday at 9:00 AM** and calculates settlement payments owed to **couriers** and **restaurants** based on delivery performance.</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">### 🔁 Logic Summary:</span></p>
<p class="p1"><span class="s1">- Fetches all **delivered orders from the past 7 days**.</span></p>
<p class="p1"><span class="s1">- Uses OpenAI to:</span></p>
<p class="p1"><span class="s1"><span class="Apple-converted-space">  </span>- Calculate delivery fee based on distance:</span></p>
<p class="p1"><span class="s1"><span class="Apple-converted-space">    </span>- &lt; 5 km → $2</span></p>
<p class="p1"><span class="s1"><span class="Apple-converted-space">    </span>- 5–7 km → $3</span></p>
<p class="p1"><span class="s1"><span class="Apple-converted-space">    </span>- ≥ 7 km → $5</span></p>
<p class="p1"><span class="s1"><span class="Apple-converted-space">  </span>- Determine if couriers made more than 5 deliveries in the same hour (which increases their share from 60% to 65%).</span></p>
<p class="p1"><span class="s1">- Calculates:</span></p>
<p class="p1"><span class="s1"><span class="Apple-converted-space">  </span>- `courier_amount`</span></p>
<p class="p1"><span class="s1"><span class="Apple-converted-space">  </span>- `company_amount`</span></p>
<p class="p1"><span class="s1">- Sends the full breakdown to **finance for approval**.</span></p>
<p class="p1"><span class="s1">- Once approved:</span></p>
<p class="p1"><span class="s1"><span class="Apple-converted-space">  </span>- Updates internal database with courier payouts.</span></p>
<p class="p1"><span class="s1"><span class="Apple-converted-space">  </span>- Uses OpenAI again to calculate **per-restaurant payout** based on a flat rate of `$20/order`.</span></p>
<p class="p1"><span class="s1"><span class="Apple-converted-space">  </span>- Sends restaurant payouts for finance approval and pushes data to external systems via API.</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">### 🖼 Workflow Screenshot:</span></p>
<p class="p1"><span class="s1">![Weekly Settlement](./WeeklySettlements.jpg)</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">---</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">## 🔁 2. RefundCustomers Workflow</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">This workflow is triggered **manually or at frequent intervals** to process **cancelled orders** and issue refunds.</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">### 🔁 Logic Summary:</span></p>
<p class="p1"><span class="s1">- Pulls all orders with `order_status = 'Cancelled'`.</span></p>
<p class="p1"><span class="s1">- Sends the list to **finance for manual approval**.</span></p>
<p class="p1"><span class="s1">- Upon approval:</span></p>
<p class="p1"><span class="s1"><span class="Apple-converted-space">  </span>- Uses a `Switch` node to determine whether the payment was made via:</span></p>
<p class="p1"><span class="s1"><span class="Apple-converted-space">    </span>- App Wallet → Refund is issued by updating wallet balance.</span></p>
<p class="p1"><span class="s1"><span class="Apple-converted-space">    </span>- Credit Card → Refund is issued through an API call to the card provider.</span></p>
<p class="p1"><span class="s1">- Confirms refund was successful.</span></p>
<p class="p1"><span class="s1">- Updates the database to reflect the refunded status and customer balance.</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">### 🖼 Workflow Screenshot:</span></p>
<p class="p1"><span class="s1">![Refund Customers](./RefundCustomers.jpg)</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">---</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">## 📁 Files Included</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">| File | Description |</span></p>
<p class="p1"><span class="s1">|------|-------------|</span></p>
<p class="p1"><span class="s1">| `WeeklySettlement.json` | n8n export for the courier/restaurant weekly payout automation |</span></p>
<p class="p1"><span class="s1">| `RefundCustomers.json` | n8n export for the customer refund workflow |</span></p>
<p class="p1"><span class="s1">| `WeeklySettlements.jpg` | Visual diagram of the WeeklySettlement flow |</span></p>
<p class="p1"><span class="s1">| `RefundCustomers.jpg` | Visual diagram of the RefundCustomers flow |</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">---</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">## 🛠 Setup Instructions</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">1. Import the `.json` files into your n8n instance.</span></p>
<p class="p1"><span class="s1">2. Ensure you have valid PostgreSQL credentials and that your database matches the required schema.</span></p>
<p class="p1"><span class="s1">3. Create API keys for any external payment/refund services if used.</span></p>
<p class="p1"><span class="s1">4. Adjust the email nodes with your internal finance team contacts.</span></p>
<p class="p1"><span class="s1">5. Optionally, connect any HTTP nodes to external accounting or ERP systems.</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">---</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">## 📌 Notes</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">- All refund and settlement logic is strictly based on the rules defined in the project document.</span></p>
<p class="p1"><span class="s1">- No extra tables or fields were introduced beyond what exists in the assignment database schema.</span></p>
<p class="p1"><span class="s1">- All sensitive operations (refunds, payouts) require finance team approval before execution.</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">---</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">## 👨‍💻 Designed and Implemented by</span></p>
<p class="p2"><span class="s1"></span><br></p>
<p class="p1"><span class="s1">**Sadegh Tajik – May 2025**</span></p>
</body>
</html>

<style>

/* Container */
.schedule-wrapper {
    padding: 8px 6px;
}

/* Header */
.schedule-title {
    text-align: center;
    margin-bottom: 15px;
}

.schedule-title h1 {
    margin: 0;
    font-size: clamp(1.4rem, 4vw, 2rem);
    background: linear-gradient(90deg, #8b2635, #d4af37);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
}

/* Grid */
.schedule-grid {
    display: grid;
    gap: 14px;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
}

/* Card */
.guru-card {
    background: var(--background-secondary);
    border-radius: 10px;
    border-top: 3px solid #d4af37;
    box-shadow: 0 3px 8px rgba(0,0,0,0.06);
}

/* Header */
.guru-header {
    padding: 10px;
    text-align: center;
    border-bottom: 1px solid var(--background-modifier-border);
}

.guru-header h2 {
    margin: 0;
    font-size: 1rem;
    color: #8b2635;
}

/* Table */
.table-container {
    padding: 6px;
}

.schedule-table {
    width: 100%;
    border-collapse: collapse;
}

.schedule-table th {
    font-size: 0.7rem;
    padding: 6px 4px;
    border-bottom: 2px solid #d4af37;
    text-align: left;
    color: var(--text-muted);
}

.schedule-table td {
    padding: 6px 4px;
    border-bottom: 1px solid var(--background-modifier-border);
    font-size: 0.85rem;
    white-space: nowrap; /* single line */
}

/* Center contact column */
.schedule-table td:last-child,
.schedule-table th:last-child {
    text-align: center;
}

/* Date column */
.date-col {
    color: #8b2635;
    font-weight: 500;
}

/* Multiple contacts */
.contact-list {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 2px;
}

.contact-number {
    font-weight: 600;
}

/* Mobile */
@media (max-width: 480px) {
    .schedule-table th,
    .schedule-table td {
        font-size: 0.8rem;
    }
}

</style>

<div class="schedule-wrapper">

<div class="schedule-title">
<h1>March 2026 · Spiritual Class Schedule</h1>
</div>

<div class="schedule-grid">

<!-- TVR -->
<div class="guru-card">
<div class="guru-header">
<h2>Brahmarshi<br>Tatavarty Veera Raghava Rao</h2>
</div>

<div class="table-container">
<table class="schedule-table">
<thead>
<tr>
<th>Date</th>
<th>Location</th>
<th>Contact</th>
</tr>
</thead>
<tbody>

<tr>
<td class="date-col">7,8,9</td>
<td>Darwada</td>
<td><span class="contact-number">9019248516</span> Sai Leela</td>
</tr>

<tr>
<td class="date-col">15-03-26</td>
<td>Kukatpally, Hyderabad</td>
<td><span class="contact-number">8368470727</span> Malleshwari</td>
</tr>

<tr>
<td class="date-col">16,17,18</td>
<td>Kadtal, Hyderabad</td>
<td>
    <div class="contact-list">
        <div><span class="contact-number">7893917364</span> Madhu</div>
        <div><span class="contact-number">9876543210</span> Srikanth</div>
    </div>
</td>
</tr>

</tbody>
</table>
</div>
</div>

<!-- TRL -->
<div class="guru-card">
<div class="guru-header">
<h2>Brahma Vidhwarishta<br>Tatavarty Rajyalakshmi</h2>
</div>

<div class="table-container">
<table class="schedule-table">
<thead>
<tr>
<th>Date</th>
<th>Location</th>
<th>Contact</th>
</tr>
</thead>
<tbody>

<tr>
<td class="date-col">16-03-26</td>
<td>Tirupathi</td>
<td><span class="contact-number">8688063157</span> Vineel</td>
</tr>

<tr>
<td class="date-col">17-03-26</td>
<td>Bengaluru</td>
<td><span class="contact-number">8179703440</span> Ganesh</td>
</tr>

<tr>
<td class="date-col">18-03-26</td>
<td>Tirupathi</td>
<td><span class="contact-number">7207112006</span> Maniteja</td>
</tr>

</tbody>
</table>
</div>
</div>

</div>
</div>        <div class="contact-item">
            <span class="contact-number">7207112006</span> · Maniteja
        </div>
    </div>
</td>
</tr>

</tbody>
</table>
</div>
</div>

</div>

</div>
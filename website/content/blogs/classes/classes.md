<style>

/* Container */
.schedule-wrapper {
    padding: 20px 10px;
}

/* Header */
.schedule-title {
    text-align: center;
    margin-bottom: 35px;
}

.schedule-title h1 {
    margin: 0;
    font-size: clamp(1.6rem, 4vw, 2.4rem);
    background: linear-gradient(90deg, #8b2635, #d4af37);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
}

/* Grid Layout */
.schedule-grid {
    display: grid;
    gap: 25px;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
}

/* Card */
.guru-card {
    background: var(--background-secondary);
    border-radius: 14px;
    border-top: 4px solid #d4af37;
    box-shadow: 0 6px 18px rgba(0,0,0,0.08);
    transition: transform 0.25s ease;
}

.guru-card:hover {
    transform: translateY(-4px);
}

/* Card Header */
.guru-header {
    padding: 18px;
    text-align: center;
    border-bottom: 1px solid var(--background-modifier-border);
}

.guru-header h2 {
    margin: 0;
    font-size: 1.1rem;
    color: #8b2635;
}

/* Table */
.table-container {
    padding: 15px;
    overflow-x: auto;
}

.schedule-table {
    width: 100%;
    border-collapse: collapse;
    min-width: 260px;
}

.schedule-table th {
    text-align: left;
    font-size: 0.75rem;
    text-transform: uppercase;
    letter-spacing: 0.5px;
    padding: 10px 6px;
    border-bottom: 2px solid #d4af37;
    color: var(--text-muted);
}

.schedule-table td {
    padding: 10px 6px;
    border-bottom: 1px solid var(--background-modifier-border);
    font-size: 0.9rem;
}

.schedule-table tr:hover {
    background: var(--background-modifier-hover);
}

/* Center Contact Column */
.schedule-table td:last-child,
.schedule-table th:last-child {
    text-align: center;
}

/* Date Column */
.date-col {
    color: #8b2635;
    font-weight: 500;
    white-space: nowrap;
}

/* Contact Styling */
.contact-list {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
}

.contact-item {
    font-size: 0.9rem;
}

.contact-number {
    font-weight: 600;
}

/* Footer */
.schedule-footer {
    text-align: center;
    margin-top: 30px;
    font-size: 0.85rem;
    color: var(--text-muted);
}

/* Mobile Fine Tuning */
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
<td>
    <div class="contact-list">
        <div class="contact-item">
            <span class="contact-number">9019248516</span> · Sai Leela
        </div>
    </div>
</td>
</tr>

<tr>
<td class="date-col">15-03-26</td>
<td>Hyderabad, Kukatpally</td>
<td>
    <div class="contact-list">
        <div class="contact-item">
            <span class="contact-number">8368470727</span> · Malleshwari
        </div>
    </div>
</td>
</tr>

<tr>
<td class="date-col">16,17,18</td>
<td>Kadtal, Hyderabad</td>
<td>
    <div class="contact-list">
        <div class="contact-item">
            <span class="contact-number">7893917364</span> · Madhu
        </div>
        <div class="contact-item">
            <span class="contact-number">9876543210</span> · Srikanth
        </div>
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
<td>
    <div class="contact-list">
        <div class="contact-item">
            <span class="contact-number">8688063157</span> · Vineel
        </div>
    </div>
</td>
</tr>

<tr>
<td class="date-col">17-03-26</td>
<td>Bengaluru</td>
<td>
    <div class="contact-list">
        <div class="contact-item">
            <span class="contact-number">8179703440</span> · Ganesh
        </div>
    </div>
</td>
</tr>

<tr>
<td class="date-col">18-03-26</td>
<td>Tirupathi</td>
<td>
    <div class="contact-list">
        <div class="contact-item">
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
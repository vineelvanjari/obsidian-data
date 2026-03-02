
<style>
:root {
    --bg-main: #fdfbf7;
    --card-bg: rgba(255,255,255,0.85);
    --accent: #8b2635;
    --gold: #d4af37;
    --text-main: #2e2e2e;
    --text-muted: #777;
    --border-light: #ece6d8;
}

.markdown-preview-view {
    background: var(--bg-main);
    background-image: radial-gradient(#efe6d5 1px, transparent 1px);
    background-size: 22px 22px;
    padding: 30px;
}

/* Header */
.month-header {
    text-align: center;
    margin-bottom: 40px;
}

.month-header h1 {
    font-family: 'Playfair Display', serif;
    font-size: clamp(1.8rem, 4vw, 2.6rem);
    background: linear-gradient(90deg, var(--accent), var(--gold));
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
}

/* Grid */
.schedule-grid {
    display: grid;
    gap: 30px;
    grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
}

/* Card */
.guru-card {
    background: var(--card-bg);
    backdrop-filter: blur(10px);
    border-radius: 16px;
    border-top: 4px solid var(--gold);
    box-shadow: 0 10px 25px rgba(0,0,0,0.08);
    transition: 0.3s ease;
}

.guru-card:hover {
    transform: translateY(-6px);
    box-shadow: 0 18px 35px rgba(139, 38, 53, 0.15);
}

.card-header {
    padding: 20px;
    text-align: center;
    border-bottom: 1px solid var(--border-light);
}

.card-header h2 {
    margin: 0;
    font-size: 1.2rem;
    color: var(--accent);
}

.table-wrapper {
    padding: 20px;
    overflow-x: auto;
}

table {
    width: 100%;
    border-collapse: collapse;
    min-width: 280px;
}

th {
    text-transform: uppercase;
    font-size: 0.75rem;
    letter-spacing: 0.5px;
    color: var(--text-muted);
    border-bottom: 2px solid var(--gold);
    padding: 12px 6px;
}

td {
    padding: 12px 6px;
    border-bottom: 1px solid var(--border-light);
    font-size: 0.9rem;
}

tbody tr:hover {
    background: #faf5ea;
}

.date {
    color: var(--accent);
    font-weight: 500;
    white-space: nowrap;
}

.contact-number {
    font-weight: 600;
}

.contact-name {
    font-style: italic;
    color: var(--text-muted);
}

/* Mobile */
@media (max-width: 500px) {
    td, th {
        font-size: 0.8rem;
    }
}
</style>

<div class="month-header">
<h1>March 2026 · Spiritual Class Schedule</h1>
<p style="color:#777; font-style:italic;">Dhyana · Atmajnana · Awareness</p>
</div>

<div class="schedule-grid">

<div class="guru-card">
<div class="card-header">
<h2>Brahmarshi<br>Tatavarty Veera Raghava Rao</h2>
</div>
<div class="table-wrapper">
<table>
<thead>
<tr>
<th>Date</th>
<th>Location</th>
<th>Contact</th>
</tr>
</thead>
<tbody>
<tr>
<td class="date">Sun, Mar 01</td>
<td>Bhimavaram, Town Hall</td>
<td><span class="contact-number">98480 12345</span> · <span class="contact-name">Ramesh</span></td>
</tr>
<tr>
<td class="date">Sun, Mar 08</td>
<td>Hyderabad, Kukatpally</td>
<td><span class="contact-number">90100 30914</span> · <span class="contact-name">Srinivas</span></td>
</tr>
<tr>
<td class="date">Sun, Mar 15</td>
<td>Vijayawada, PMC Center</td>
<td><span class="contact-number">83684 70727</span> · <span class="contact-name">Kiran</span></td>
</tr>
</tbody>
</table>
</div>
</div>

<div class="guru-card">
<div class="card-header">
<h2>Brahma Vidhwarishta<br>Tatavarty Rajyalakshmi</h2>
</div>
<div class="table-wrapper">
<table>
<thead>
<tr>
<th>Date</th>
<th>Location</th>
<th>Contact</th>
</tr>
</thead>
<tbody>
<tr>
<td class="date">Sat, Mar 07</td>
<td>Bhimavaram, Pyramid Center</td>
<td><span class="contact-number">98480 12345</span> · <span class="contact-name">Ramesh</span></td>
</tr>
<tr>
<td class="date">Sat, Mar 14</td>
<td>Guntur, Spiritual Society</td>
<td><span class="contact-number">90100 30914</span> · <span class="contact-name">Srinivas</span></td>
</tr>
<tr>
<td class="date">Wed, Mar 25</td>
<td>Online (Zoom Session)</td>
<td><span class="contact-number">93962 93315</span> · <span class="contact-name">Support</span></td>
</tr>
</tbody>
</table>
</div>
</div>

</div>

<div style="text-align:center; margin-top:40px; font-size:0.85rem; color:#777;">
</div>            background-image: radial-gradient(#f0eadd 1px, transparent 1px);
            background-size: 20px 20px;
            color: var(--text-main);
            margin: 0;
            padding: 40px 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        .header {
            text-align: center;
            margin-bottom: 50px;
        }

        .header h1 {
            font-family: 'Playfair Display', serif;
            color: var(--accent-color);
            font-size: 2.5rem;
            margin: 0;
            letter-spacing: 1px;
        }

        .header p {
            color: var(--text-muted);
            font-size: 1.1rem;
            margin-top: 10px;
            font-style: italic;
        }

        .schedule-container {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(450px, 1fr));
            gap: 40px;
            width: 100%;
            max-width: 1200px;
        }

        .guru-card {
            background-color: var(--card-bg);
            border-radius: 12px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.05);
            overflow: hidden;
            position: relative;
            border-top: 4px solid var(--gold-accent);
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }

        .guru-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 15px 35px rgba(139, 38, 53, 0.1);
        }

        .card-header {
            background: linear-gradient(to right, #ffffff, #fdfbf7);
            padding: 25px 30px;
            border-bottom: 1px solid var(--table-border);
            text-align: center;
        }

        .card-header h2 {
            font-family: 'Playfair Display', serif;
            color: var(--accent-color);
            font-size: 1.4rem;
            margin: 0;
            line-height: 1.4;
        }

        .table-container {
            padding: 20px 30px 30px;
            overflow-x: auto;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            text-align: left;
        }

        th {
            font-family: 'Playfair Display', serif;
            color: var(--text-muted);
            font-weight: 600;
            font-size: 0.95rem;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            padding: 15px 10px;
            border-bottom: 2px solid var(--gold-accent);
        }

        td {
            padding: 16px 10px;
            border-bottom: 1px solid var(--table-border);
            font-size: 1rem;
            color: var(--text-main);
        }

        tbody tr {
            transition: background-color 0.2s ease;
        }

        tbody tr:hover {
            background-color: #fcf9f2;
        }

        tbody tr:last-child td {
            border-bottom: none;
        }

        .date-col {
            font-weight: 500;
            color: var(--accent-color);
            white-space: nowrap;
        }

        .contact-col {
            font-size: 0.95rem;
        }
        
        .contact-number {
            font-weight: 600;
            color: #444;
        }

        .contact-name {
            color: var(--text-muted);
            font-style: italic;
        }

        /* Responsive adjustments */
        @media (max-width: 600px) {
            .schedule-container {
                grid-template-columns: 1fr;
            }
            .card-header h2 {
                font-size: 1.2rem;
            }
            td, th {
                padding: 12px 8px;
                font-size: 0.9rem;
            }
        }
    </style>
</head>
<body>

    <div class="header">
        <h1>Monthly Class Schedule</h1>
        <p>March 2026 · Dhyana & Atmajnana Sandesham</p>
    </div>

    <div class="schedule-container">
        
        <div class="guru-card">
            <div class="card-header">
                <h2>‘Brahmarshi’<br>Tatavarty Veera Raghava Rao</h2>
            </div>
            <div class="table-container">
                <table>
                    <thead>
                        <tr>
                            <th>Date</th>
                            <th>Location</th>
                            <th>Contact</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr>
                            <td class="date-col">Sun, Mar 01</td>
                            <td>Bhimavaram, Town Hall</td>
                            <td class="contact-col"><span class="contact-number">98480 12345</span> · <span class="contact-name">Ramesh</span></td>
                        </tr>
                        <tr>
                            <td class="date-col">Sat, Mar 08</td>
                            <td>Vijayawada, PMC Center</td>
                            <td class="contact-col"><span class="contact-number">90100 30914</span> · <span class="contact-name">Srinivas</span></td>
                        </tr>
                        <tr>
                            <td class="date-col">Sun, Mar 15</td>
                            <td>Kukatpally, Hyderabad</td>
                            <td class="contact-col"><span class="contact-number">83684 70727</span> · <span class="contact-name">Kiran</span></td>
                        </tr>
                        <tr>
                            <td class="date-col">Sun, Mar 22</td>
                            <td>Rajahmundry, River Front</td>
                            <td class="contact-col"><span class="contact-number">93962 93315</span> · <span class="contact-name">Lakshmi</span></td>
                        </tr>
                        <tr>
                            <td class="date-col">Sun, Mar 29</td>
                            <td>Vizag, Seethammadhara</td>
                            <td class="contact-col"><span class="contact-number">99887 76655</span> · <span class="contact-name">Venkat</span></td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>

        <div class="guru-card">
            <div class="card-header">
                <h2>‘Brahma Vidhwarishta’<br>Tatavarty Rajyalakshmi</h2>
            </div>
            <div class="table-container">
                <table>
                    <thead>
                        <tr>
                            <th>Date</th>
                            <th>Location</th>
                            <th>Contact</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr>
                            <td class="date-col">Sat, Mar 07</td>
                            <td>Bhimavaram, Pyramid Center</td>
                            <td class="contact-col"><span class="contact-number">98480 12345</span> · <span class="contact-name">Ramesh</span></td>
                        </tr>
                        <tr>
                            <td class="date-col">Sat, Mar 14</td>
                            <td>Guntur, Spiritual Society</td>
                            <td class="contact-col"><span class="contact-number">90100 30914</span> · <span class="contact-name">Srinivas</span></td>
                        </tr>
                        <tr>
                            <td class="date-col">Sat, Mar 21</td>
                            <td>Kukatpally, Hyderabad</td>
                            <td class="contact-col"><span class="contact-number">83684 70727</span> · <span class="contact-name">Kiran</span></td>
                        </tr>
                        <tr>
                            <td class="date-col">Wed, Mar 25</td>
                            <td>Online (Zoom Session)</td>
                            <td class="contact-col"><span class="contact-number">93962 93315</span> · <span class="contact-name">Support</span></td>
                        </tr>
                        <tr>
                            <td class="date-col">Sat, Mar 28</td>
                            <td>Vizag, MVP Colony</td>
                            <td class="contact-col"><span class="contact-number">99887 76655</span> · <span class="contact-name">Venkat</span></td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>

    </div>

</body>
</html>

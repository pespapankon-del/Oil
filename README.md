<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>เครื่องคำนวณน้ำมันฉบับสมบูรณ์ (All-in-One)</title>
    <style>
        body { font-family: 'Tahoma', 'Arial', sans-serif; background-color: #f0f2f5; display: flex; justify-content: center; padding: 20px; }
        .card { background: white; padding: 30px; border-radius: 20px; box-shadow: 0 10px 30px rgba(0,0,0,0.1); width: 100%; max-width: 600px; }
        
        .lang-bar { display: flex; justify-content: flex-end; gap: 8px; margin-bottom: 20px; }
        .lang-btn { padding: 6px 12px; border: 1px solid #ddd; border-radius: 8px; cursor: pointer; font-size: 13px; background: #fff; transition: 0.3s; }
        .lang-btn.active { background: #27ae60; color: white; border-color: #27ae60; }

        h2 { color: #2c3e50; text-align: center; margin-top: 0; border-bottom: 3px solid #27ae60; padding-bottom: 10px; }
        .step-title { font-weight: bold; color: #2ecc71; margin-top: 25px; margin-bottom: 12px; display: block; font-size: 1.1em; }
        
        /* ปุ่ม Step 1 */
        .grid-box { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 12px; }
        .item-btn { border: 2px solid #eee; border-radius: 15px; padding: 20px 10px; text-align: center; cursor: pointer; transition: 0.3s; background: #fff; }
        .item-btn:hover { border-color: #2ecc71; transform: translateY(-3px); }
        .item-btn.active { border-color: #27ae60; background-color: #e8f6ef; font-weight: bold; box-shadow: 0 4px 10px rgba(39,174,96,0.2); }

        /* ปุ่ม Step 2 (ยี่ห้อ) */
        .brand-grid { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 8px; margin-top: 10px; }
        .brand-btn { border: 1px solid #ddd; padding: 12px 5px; border-radius: 10px; text-align: center; cursor: pointer; font-size: 13px; background: #f9f9f9; transition: 0.2s; }
        .brand-btn:hover { background: #eee; }
        .brand-btn.active { background: #2ecc71; color: white; border-color: #27ae60; }

        /* Dropdown รุ่นรถ */
        .model-select { width: 100%; padding: 12px; border: 2px solid #2ecc71; border-radius: 10px; font-size: 16px; margin-top: 10px; outline: none; cursor: pointer; }

        /* Input ข้อมูล */
        .input-group { margin-top: 20px; display: none; }
        input { width: 100%; padding: 14px; margin: 10px 0; border: 1px solid #ddd; border-radius: 10px; box-sizing: border-box; font-size: 16px; }
        .checkbox-group { display: flex; align-items: center; margin: 15px 0; font-size: 15px; color: #555; cursor: pointer; }
        .checkbox-group input { width: auto; margin-right: 10px; cursor: pointer; }

        button#calcBtn { width: 100%; background: linear-gradient(135deg, #2ecc71, #27ae60); color: white; border: none; padding: 18px; border-radius: 12px; font-size: 20px; cursor: pointer; font-weight: bold; margin-top: 15px; transition: 0.3s; box-shadow: 0 4px 15px rgba(39,174,96,0.3); }
        button#calcBtn:hover { transform: scale(1.02); filter: brightness(1.1); }

        /* ผลลัพธ์ */
        #result { margin-top: 30px; padding: 25px; border-radius: 15px; background-color: #e8f6ef; border: 2px solid #27ae60; display: none; text-align: center; animation: fadeIn 0.5s; }
        .highlight { font-size: 32px; font-weight: bold; color: #27ae60; }
        
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body>

<div class="card">
    <div class="lang-bar">
        <div class="lang-btn active" onclick="changeLang('th', this)">TH</div>
        <div class="lang-btn" onclick="changeLang('en', this)">EN</div>
    </div>

    <h2 id="txt-title">เครื่องคำนวณงบน้ำมัน</h2>

    <span class="step-title" id="txt-step1">Step 1: เลือกประเภทพาหนะ</span>
    <div class="grid-box">
        <div class="item-btn" id="type-car" onclick="filterBrands('car', this)">🚗 <br><span id="txt-car">รถยนต์</span></div>
        <div class="item-btn" id="type-moto" onclick="filterBrands('moto', this)">🛵 <br><span id="txt-moto">มอเตอร์ไซค์</span></div>
        <div class="item-btn" id="type-agri" onclick="filterBrands('agri', this)">🚜 <br><span id="txt-agri">รถเกษตร</span></div>
    </div>

    <div id="brandSection" style="display:none;">
        <span class="step-title" id="txt-step2">Step 2: เลือกยี่ห้อหรือกลุ่ม</span>
        <div class="brand-grid" id="brandList"></div>
        
        <div id="modelDiv" style="display:none;">
            <span class="step-title">เลือกรุ่นที่ต้องการ</span>
            <select class="model-select" id="modelSelect" onchange="selectModel()">
                <option value="">-- กรุณาเลือกรุ่น --</option>
            </select>
        </div>
    </div>

    <div id="inputSection" class="input-group">
        <span class="step-title" id="txt-step3">Step 3: รายละเอียดการเดินทาง</span>
        <label id="txt-fuelPrice">ราคาน้ำมันปัจจุบัน (บาท/ลิตร):</label>
        <input type="number" id="fuelPrice" step="0.1" placeholder="เช่น 38.45">
        
        <label id="txt-dist">ระยะทางที่ต้องการเดินทาง (กิโลเมตร):</label>
        <input type="number" id="distance" placeholder="เช่น 250">

        <label class="checkbox-group">
            <input type="checkbox" id="heavyLoad">
            <span id="txt-heavy">บรรทุกหนัก / เปิดแอร์แรง (+20%)</span>
        </label>

        <button id="calcBtn" onclick="calculate()">คำนวณงบประมาณ</button>
    </div>

    <div id="result">
        <div id="resName" style="font-weight:bold; font-size: 1.2em; color: #2c3e50; margin-bottom:10px;"></div>
        <div id="txt-resLiters">ปริมาณน้ำมันที่คาดว่าต้องใช้: <br><span id="outLiters" class="highlight">0</span> ลิตร</div>
        <hr style="border: 0.5px solid #bddbc3; margin: 15px 0;">
        <div id="txt-resMoney">งบประมาณที่ควรเตรียม: <br><span id="outMoney" class="highlight">0</span> บาท</div>
    </div>
</div>

<script>
    const translations = {
        'th': { title: "เครื่องคำนวณงบน้ำมัน", s1: "Step 1: เลือกประเภทพาหนะ", s2: "Step 2: เลือกยี่ห้อหรือกลุ่ม", s3: "Step 3: รายละเอียดการเดินทาง", car: "รถยนต์", moto: "มอเตอร์ไซค์", agri: "รถเกษตร", price: "ราคาน้ำมันปัจจุบัน (บาท/ลิตร):", dist: "ระยะทางเดินทาง (กม.):", heavy: "บรรทุกหนัก / เปิดแอร์แรง (+20%)", btn: "คำนวณงบประมาณ", resL: "ปริมาณน้ำมันที่ใช้:", resM: "งบประมาณที่ควรเตรียม:" },
        'en': { title: "Fuel Budget Planner", s1: "Step 1: Vehicle Type", s2: "Step 2: Brand & Category", s3: "Step 3: Trip Details", car: "Car", moto: "Motorcycle", agri: "Agri-Vehicle", price: "Fuel Price (Baht/L):", dist: "Distance (km):", heavy: "Heavy Load / High AC (+20%)", btn: "Calculate Now", resL: "Fuel Needed:", resM: "Estimated Budget:" }
    };

    // รวมข้อมูลทั้งหมดที่คุณให้มา
    const vehicleData = {
        'car': {
            'TOYOTA': [
                { name: "Yaris / Yaris Ativ", kml: 23.3 }, { name: "Corolla Altis", kml: 17.0 }, { name: "Camry", kml: 15.6 },
                { name: "Corolla Cross / Yaris Cross", kml: 23.3 }, { name: "Fortuner (PPV)", kml: 11.8 }, { name: "Hilux Revo / Champ", kml: 12.5 },
                { name: "Alphard / Vellfire / Veloz", kml: 11.2 }
            ],
            'HONDA': [
                { name: "City (Turbo / e:HEV)", kml: 23.8 }, { name: "Civic", kml: 17.2 }, { name: "Accord", kml: 16.5 },
                { name: "WR-V / BR-V / HR-V / CR-V", kml: 15.8 }
            ],
            'ISUZU': [
                { name: "D-Max (V-Cross/Spark/Cab4)", kml: 13.5 }, { name: "MU-X", kml: 12.2 }
            ],
            'MAZDA': [
                { name: "Mazda 2", kml: 26.3 }, { name: "Mazda 3", kml: 15.8 }, { name: "CX-3 / CX-30 / CX-5 / CX-8", kml: 13.5 }
            ],
            'MITSUBISHI': [
                { name: "Mirage / Attrage", kml: 23.3 }, { name: "Xpander / Xpander Cross", kml: 14.5 }, { name: "Pajero Sport", kml: 11.5 }, { name: "Triton", kml: 12.4 }
            ],
            'NISSAN': [
                { name: "Almera", kml: 23.3 }, { name: "Kicks e-POWER", kml: 23.8 }, { name: "Terra (PPV)", kml: 12.1 }, { name: "Navara", kml: 13.5 }
            ],
            'FORD': [
                { name: "Ranger / Ranger Raptor", kml: 10.5 }, { name: "Everest", kml: 11.2 }
            ],
            'LUXURY': [
                { name: "Mercedes-Benz (C/E/S/GLC)", kml: 14.5 }, { name: "BMW (Series 3/5/7/X1/X3)", kml: 15.2 }, { name: "Volvo (XC40/60/90)", kml: 16.5 }
            ]
        },
        'moto': {
            'รถครอบครัว': [
                { name: "Honda Wave 110i / 125i", kml: 76.9 }, { name: "Yamaha Finn", kml: 96.9 }, { name: "Honda Super Cub", kml: 60.1 }
            ],
            'รถออโตเมติก': [
                { name: "Honda GIORNO+", kml: 53.0 }, { name: "Yamaha Grand Filano Hybrid", kml: 62.5 }, { name: "Honda Scoopy / Yamaha Fazzio", kml: 55.0 }, { name: "Honda Lead 125", kml: 52.6 }
            ],
            'สปอร์ตสกู๊ตเตอร์': [
                { name: "Honda PCX 160 / Yamaha NMAX", kml: 45.0 }, { name: "Honda Forza 350 / Yamaha XMAX", kml: 30.0 }, { name: "Honda ADV350", kml: 28.5 }
            ],
            'สายลุย/ไลฟ์สไตล์': [
                { name: "Honda CRF300L / Rally", kml: 26.5 }, { name: "Yamaha PG-1", kml: 48.2 }, { name: "Honda Monkey / CT125", kml: 60.5 }
            ],
            'บิ๊กไบค์': [
                { name: "Honda CBR / Yamaha R-Series", kml: 22.0 }, { name: "Kawasaki Ninja / Z Series", kml: 19.5 }
            ]
        },
        'agri': {
            'เครื่องจักร': [
                { name: "รถไถ (Tractor)", kml: 6.0 }, { name: "รถเกี่ยวข้าว", kml: 4.5 }, { name: "ซาเล้ง (Saleng)", kml: 30.0 }
            ]
        }
    };

    let selectedKml = 0;
    let selectedName = "";

    function changeLang(lang, btn) {
        document.querySelectorAll('.lang-btn').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        const t = translations[lang];
        document.getElementById('txt-title'

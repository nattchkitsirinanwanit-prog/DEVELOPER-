<!DOCTYPE html>
<html lang="th">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Eternal Love | 3D Bouquet & 3D Character Studio</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>
<style>
* {
margin: 0;
padding: 0;
box-sizing: border-box;
font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

body {
min-height: 100vh;
display: flex;
flex-direction: column;
align-items: center;
background: radial-gradient(circle at top, #2b1055 0%, #16092e 55%, #090414 100%);
overflow-x: hidden;
padding: 20px;
color: #fff;
}

#particles {
position: fixed;
top: 0; left: 0;
width: 100%; height: 100%;
z-index: 1;
pointer-events: none;
}

/* Toast Notification มุมซ้ายบน */
#toastNotification {
position: fixed;
top: 20px;
left: 20px;
background: rgba(22, 9, 46, 0.92);
backdrop-filter: blur(12px);
border: 1px solid rgba(255, 117, 140, 0.6);
color: #ffccd5;
padding: 12px 22px;
border-radius: 12px;
font-size: 0.9rem;
font-weight: 600;
box-shadow: 0 8px 25px rgba(0, 0, 0, 0.6);
z-index: 9999;
opacity: 0;
transform: translateX(-30px);
transition: all 0.35s cubic-bezier(0.34, 1.56, 0.64, 1);
pointer-events: none;
}

#toastNotification.show {
opacity: 1;
transform: translateX(0);
}

.container {
position: relative;
z-index: 2;
width: 100%;
max-width: 900px;
display: flex;
flex-direction: column;
gap: 24px;
align-items: center;
}

.glass-card {
background: rgba(255, 255, 255, 0.06);
backdrop-filter: blur(20px);
-webkit-backdrop-filter: blur(20px);
border: 1px solid rgba(255, 255, 255, 0.14);
border-radius: 24px;
padding: 28px;
width: 100%;
box-shadow: 0 20px 50px rgba(0, 0, 0, 0.5);
text-align: center;
}

.rgb-title {
font-size: 2.3rem;
margin-bottom: 6px;
letter-spacing: 1.5px;
text-transform: uppercase;
font-weight: 800;
color: transparent;
background: linear-gradient(270deg, #ff758c, #ff7eb3, #ffffff, #a18cd1);
background-size: 300% auto;
-webkit-background-clip: text;
background-clip: text;
-webkit-text-fill-color: transparent;
animation: shine 5s linear infinite;
}

@keyframes shine {
to { background-position: 300% center; }
}

p.subtitle {
font-size: 0.95rem;
color: #e2e8f0;
margin-bottom: 12px;
}

/* 3D WebGL Canvas Container */
#canvas3d-container {
position: relative;
width: 100%;
height: 480px;
border-radius: 20px;
overflow: hidden;
background: radial-gradient(circle at center, rgba(255, 117, 140, 0.15) 0%, rgba(0, 0, 0, 0.45) 100%);
border: 1px solid rgba(255, 255, 255, 0.1);
cursor: grab;
margin: 10px 0;
}

#canvas3d-container:active {
cursor: grabbing;
}

/* กรอบคำพูด 3D Floating Speech Bubble */
.speech-bubble {
position: absolute;
background: rgba(255, 255, 255, 0.94);
backdrop-filter: blur(10px);
color: #1a1638;
padding: 10px 16px;
border-radius: 16px 16px 16px 4px;
max-width: 200px;
font-size: 0.85rem;
font-weight: 600;
box-shadow: 0 10px 25px rgba(0,0,0,0.4);
pointer-events: none;
z-index: 10;
transform: translate(-50%, -130%);
transition: opacity 0.2s;
word-break: break-word;
text-align: left;
}

.speech-bubble::after {
content: '';
position: absolute;
bottom: -6px;
left: 15px;
width: 0; height: 0;
border-left: 6px solid transparent;
border-right: 6px solid transparent;
border-top: 8px solid rgba(255, 255, 255, 0.94);
}

/* Controls Grid */
.controls-grid {
display: grid;
grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
gap: 15px;
margin-top: 15px;
text-align: left;
}

.control-group {
display: flex;
flex-direction: column;
gap: 6px;
}

label {
font-size: 0.85rem;
color: #ffccd5;
font-weight: 600;
display: flex;
justify-content: space-between;
align-items: center;
}

select, input[type="text"], textarea {
width: 100%;
padding: 12px;
background: rgba(255, 255, 255, 0.1);
border: 1px solid rgba(255, 255, 255, 0.2);
border-radius: 12px;
color: #fff;
outline: none;
font-size: 0.95rem;
transition: border-color 0.2s;
}

select:focus, input[type="text"]:focus, textarea:focus {
border-color: #ff758c;
}

select option {
background: #1a1638;
color: #fff;
}

/* Range Slider & Mini Button */
.range-row {
display: flex;
align-items: center;
gap: 10px;
background: rgba(255, 255, 255, 0.1);
border: 1px solid rgba(255, 255, 255, 0.2);
border-radius: 12px;
padding: 10px 14px;
height: 45px;
}

input[type="range"] {
flex: 1;
accent-color: #ff758c;
cursor: pointer;
}

.count-badge {
background: #ff758c;
color: #fff;
padding: 2px 10px;
border-radius: 20px;
font-size: 0.85rem;
font-weight: bold;
min-width: 58px;
text-align: center;
}

.arrange-row {
display: flex;
gap: 8px;
}

.btn-mini {
background: rgba(255, 117, 140, 0.25);
border: 1px solid rgba(255, 117, 140, 0.6);
color: #ffccd5;
padding: 0 14px;
border-radius: 12px;
font-size: 0.85rem;
font-weight: 600;
cursor: pointer;
white-space: nowrap;
transition: all 0.2s;
}

.btn-mini:hover {
background: #ff758c;
color: #fff;
}

/* Realistic 3D Envelope & Pull-out Letter */
.envelope-section {
display: flex;
flex-direction: column;
align-items: center;
width: 100%;
padding-top: 20px;
}

.envelope-wrapper {
position: relative;
width: 340px;
height: 220px;
cursor: pointer;
perspective: 1200px;
margin: 20px auto 10px auto;
}

.envelope-back {
position: absolute;
bottom: 0;
width: 100%;
height: 160px;
background: #9e192d;
border-radius: 8px 8px 16px 16px;
box-shadow: 0 20px 40px rgba(0,0,0,0.5);
z-index: 1;
}

.letter-paper {
position: absolute;
bottom: 12px;
left: 18px;
right: 18px;
height: 170px;
background: linear-gradient(to bottom, #fffdfa, #f7f1e3);
color: #2c2c2c;
padding: 18px;
border-radius: 8px;
box-shadow: 0 5px 20px rgba(0,0,0,0.25);
z-index: 2;
transition: transform 0.7s cubic-bezier(0.34, 1.56, 0.64, 1), z-index 0.3s;
transform: translateY(0);
border: 1px solid #e2d9c8;
}

.envelope-front-left {
position: absolute;
bottom: 0; left: 0;
width: 0; height: 0;
border-left: 175px solid #d92638;
border-top: 85px solid transparent;
border-bottom: 75px solid transparent;
border-radius: 0 0 0 16px;
z-index: 3;
pointer-events: none;
}

.envelope-front-right {
position: absolute;
bottom: 0; right: 0;
width: 0; height: 0;
border-right: 175px solid #c91e30;
border-top: 85px solid transparent;
border-bottom: 75px solid transparent;
border-radius: 0 0 16px 0;
z-index: 3;
pointer-events: none;
}

.envelope-front-bottom {
position: absolute;
bottom: 0; left: 0;
width: 0; height: 0;
border-left: 170px solid transparent;
border-right: 170px solid transparent;
border-bottom: 105px solid #e63946;
border-radius: 0 0 16px 16px;
z-index: 4;
pointer-events: none;
filter: drop-shadow(0 -3px 5px rgba(0,0,0,0.15));
}

.envelope-flap {
position: absolute;
top: 60px; left: 0;
width: 0; height: 0;
border-left: 170px solid transparent;
border-right: 170px solid transparent;
border-top: 105px solid #b51728;
transform-origin: top;
transition: transform 0.6s ease, z-index 0.3s;
z-index: 5;
}

.wax-seal {
position: absolute;
top: 145px;
left: 148px;
width: 44px;
height: 44px;
background: radial-gradient(circle, #ff4d6d, #800f2f);
border-radius: 50%;
z-index: 6;
display: flex;
justify-content: center;
align-items: center;
font-size: 1.2rem;
box-shadow: 0 4px 10px rgba(0,0,0,0.4);
transition: opacity 0.3s, transform 0.3s;
}

.envelope-wrapper.open .wax-seal {
opacity: 0;
transform: scale(0.5);
pointer-events: none;
}

.envelope-wrapper.open .envelope-flap {
transform: rotateX(180deg);
z-index: 1;
}

.envelope-wrapper.open .letter-paper {
transform: translateY(-115px);
z-index: 6;
box-shadow: 0 15px 35px rgba(0,0,0,0.4);
}

/* Photo Grid */
.photo-grid {
display: grid;
grid-template-columns: repeat(3, 1fr);
gap: 14px;
margin-top: 15px;
}

@media (max-width: 600px) {
.photo-grid { grid-template-columns: repeat(2, 1fr); }
.envelope-wrapper { transform: scale(0.88); }
#canvas3d-container { height: 400px; }
}

.photo-slot {
width: 100%;
height: 140px;
border: 2px dashed rgba(255,255,255,0.25);
border-radius: 14px;
display: flex;
justify-content: center;
align-items: center;
position: relative;
overflow: hidden;
background: rgba(0,0,0,0.2);
cursor: pointer;
transition: all 0.25s;
}

.photo-slot:hover {
transform: scale(1.02);
border-color: #ff758c;
}

.photo-slot img {
width: 100%;
height: 100%;
object-fit: cover;
}

/* Button & Music */
.btn {
margin-top: 15px;
background: linear-gradient(135deg, #ff758c, #ff7eb3);
color: white;
border: none;
padding: 12px 28px;
border-radius: 30px;
font-weight: bold;
font-size: 1rem;
cursor: pointer;
box-shadow: 0 5px 15px rgba(255, 117, 140, 0.4);
transition: all 0.25s ease;
}

.btn:hover {
transform: translateY(-2px);
box-shadow: 0 8px 20px rgba(255, 117, 140, 0.65);
}

.music-box {
display: flex;
flex-direction: column;
gap: 10px;
text-align: left;
}

.music-row {
display: flex;
gap: 10px;
}
</style>
</head>
<body>

<canvas id="particles"></canvas>

<!-- Toast Notification มุมซ้ายบน -->
<div id="toastNotification">✨ เซฟเรียบร้อย! คัดลอกลิงก์แล้ว</div>

<div class="container">
<!-- Header & True 3D Studio + 3D Character -->
<div class="glass-card">
<h1 class="rgb-title">Eternal Love 3D Studio</h1>
<p class="subtitle">"ช่อดอกไม้ 3D และตัวละคร 3 มิติยืนข้างกัน หมุนดูได้รอบทิศทาง 360°"</p>
<div id="canvas3d-container">
<!-- กรอบคำพูด 3D ลอยเหนือตัวละคร 3D -->
<div class="speech-bubble" id="speechBubble">สุขสันต์วันพิเศษนะคนดี! ❤️</div>
</div>
<p style="font-size: 0.85rem; color: #ffd1dc; margin-top: 8px;">💡 คลิกค้างแล้วลากเพื่อหมุนฉาก 3D / เลื่อนลูกกลิ้งเมาส์เพื่อซูมเข้า-ออก</p>
</div>

<!-- Customization Panel -->
<div class="glass-card">
<h2 style="font-size: 1.25rem; margin-bottom: 12px; color: #ffe5ec;">🎨 จัดช่อดอกไม้และปรับแต่งตัวละคร 3D</h2>
<div class="controls-grid">
<div class="control-group">
<label>เลือกตัวละคร 3D ยืนข้างช่อดอกไม้:</label>
<select id="characterSelect" onchange="rebuild3DCharacter()">
<option value="0">🧸 น้องหมีสีน้ำตาลขี้อ้อน</option>
<option value="1">🐱 น้องแมวเหมียวสุดน่ารัก</option>
<option value="2">🐰 น้องกระต่ายหูยาว</option>
<option value="3">🤖 บอทหุ่นยนต์เพื่อนซี้</option>
<option value="4">🦄 ยูนิคอร์นแฟนตาซี</option>
</select>
</div>

<div class="control-group">
<label>ข้อความให้ตัวละคร 3D พูด:</label>
<input type="text" id="characterMessageInput" value="สุขสันต์วันพิเศษนะคนดี! ❤️" oninput="updateSpeechText()" placeholder="พิมพ์ข้อความให้ตัวละครพูด...">
</div>

<div class="control-group">
<label>เลือกชนิดดอกไม้ (30 ชนิด):</label>
<select id="flowerSelect" onchange="rebuild3DBouquet()"></select>
</div>

<div class="control-group">
<label>
<span>จำนวนดอกไม้ในช่อ:</span>
</label>
<div class="range-row">
<input type="range" id="flowerCountSlider" min="1" max="35" value="19" oninput="updateFlowerCount()">
<span class="count-badge" id="flowerCountLabel">19 ดอก</span>
</div>
</div>

<div class="control-group">
<label>รูปแบบการจัดเรียงดอกไม้:</label>
<div class="arrange-row">
<select id="arrangementSelect" onchange="rebuild3DBouquet()">
<option value="dome">1. ทรงกลมคลาสสิก (Round Dome)</option>
<option value="heart">2. ทรงหัวใจโรแมนติก (Heart Shape)</option>
<option value="cascade">3. ทรงไล่ระดับลดหลั่น (Cascade)</option>
<option value="minimal">4. ทรงมินิมอลโปร่งสบาย (Minimalist)</option>
<option value="random">5. จัดแบบสุ่มอิสระ (Random Artistic)</option>
</select>
<button type="button" class="btn-mini" onclick="randomizeArrangement()" title="สุ่มตำแหน่งใหม่">🎲 สุ่มจัด</button>
</div>
</div>

<div class="control-group">
<label>เลือกสีริบบิ้น (20 สี):</label>
<select id="ribbonSelect" onchange="rebuild3DBouquet()"></select>
</div>

<div class="control-group">
<label>เลือกสไตล์กระดาษห่อช่อ (20 แบบ):</label>
<select id="bouquetSelect" onchange="rebuild3DBouquet()"></select>
</div>

<div class="control-group">
<label>เลือกเอฟเฟกต์บรรยากาศ (5 แบบ):</label>
<select id="effectSelect" onchange="updateEffect()"></select>
</div>
</div>
</div>

<!-- Realistic 3D Envelope & Letter -->
<div class="glass-card">
<h2 style="font-size: 1.25rem; margin-bottom: 8px; color: #ffe5ec;">💌 จดหมายรักในซอง (คลิกที่ซองเพื่อเปิดอ่าน)</h2>
<div class="envelope-section">
<div class="envelope-wrapper" id="envelopeCard" onclick="toggleEnvelope()">
<div class="envelope-back"></div>
<div class="letter-paper" onclick="event.stopPropagation()">
<textarea id="loveMessage" style="width:100%; height:100%; border:none; background:transparent; color:#2c2c2c; outline:none; resize:none; font-size:0.95rem; line-height:1.5;" placeholder="พิมพ์ข้อความความในใจที่นี่...">ช่อดอกไม้นี้สวยและอยู่คงทนตลอดไป เช่นเดียวกับหัวใจที่มีให้เธอเสมอนะ ❤️</textarea>
</div>
<div class="envelope-front-left"></div>
<div class="envelope-front-right"></div>
<div class="envelope-front-bottom"></div>
<div class="envelope-flap"></div>
<div class="wax-seal">❤️</div>
</div>
<p style="font-size: 0.8rem; color: #ffd1dc;">แตะที่ซองจดหมายเพื่อเปิด/ปิด และพิมพ์ข้อความในกระดาษได้ทันที</p>
</div>
</div>

<!-- Couple Photo Gallery -->
<div class="glass-card">
<h2 style="font-size: 1.25rem; margin-bottom: 8px; color: #ffe5ec;">🖼️ แกลเลอรีรูปคู่ความทรงจำ (6 ช่อง)</h2>
<div class="photo-grid">
<div class="photo-slot" onclick="triggerUpload(1)"><span style="font-size:0.85rem; color:#ffd1dc;">+ รูปที่ 1</span><input type="file" id="up1" accept="image/*" style="display:none" onchange="loadPhoto(event, 1)"></div>
<div class="photo-slot" onclick="triggerUpload(2)"><span style="font-size:0.85rem; color:#ffd1dc;">+ รูปที่ 2</span><input type="file" id="up2" accept="image/*" style="display:none" onchange="loadPhoto(event, 2)"></div>
<div class="photo-slot" onclick="triggerUpload(3)"><span style="font-size:0.85rem; color:#ffd1dc;">+ รูปที่ 3</span><input type="file" id="up3" accept="image/*" style="display:none" onchange="loadPhoto(event, 3)"></div>
<div class="photo-slot" onclick="triggerUpload(4)"><span style="font-size:0.85rem; color:#ffd1dc;">+ รูปที่ 4</span><input type="file" id="up4" accept="image/*" style="display:none" onchange="loadPhoto(event, 4)"></div>
<div class="photo-slot" onclick="triggerUpload(5)"><span style="font-size:0.85rem; color:#ffd1dc;">+ รูปที่ 5</span><input type="file" id="up5" accept="image/*" style="display:none" onchange="loadPhoto(event, 5)"></div>
<div class="photo-slot" onclick="triggerUpload(6)"><span style="font-size:0.85rem; color:#ffd1dc;">+ รูปที่ 6</span><input type="file" id="up6" accept="image/*" style="display:none" onchange="loadPhoto(event, 6)"></div>
</div>
</div>

<!-- YouTube Music Player & Save/Share Button -->
<div class="glass-card">
<div class="music-box">
<label>🎵 ลิงก์เพลงประกอบ (YouTube URL):</label>
<div class="music-row">
<input type="text" id="musicUrl" placeholder="วางลิงก์ YouTube เช่น https://www.youtube.com/watch?v=...">
<button class="btn" style="margin-top:0; white-space:nowrap;" onclick="playMusic()">เล่นเพลง</button>
</div>
<div id="youtubePlayerWrapper" style="width: 100%; margin-top: 5px;"></div>
</div>
<div style="text-align: center; margin-top: 20px;">
<button class="btn" onclick="saveAndCopy()">🎁 บันทึกและคัดลอกลิงก์ส่งให้แฟน</button>
</div>
</div>
</div>

<script>
const flowerData = [
{ name: "กุหลาบแดงคลาสสิก", color: 0xe60026, inner: 0x800013, type: "rose" },
{ name: "ทิวลิปชมพูพาสเทล", color: 0xff69b4, inner: 0xc71585, type: "tulip" },
{ name: "ทานตะวันสดใส", color: 0xffd700, inner: 0x3e2723, type: "sunflower" },
{ name: "ซากุระญี่ปุ่น", color: 0xffb7c5, inner: 0xff69b4, type: "sakura" },
{ name: "ไฮเดรนเยียฟ้า", color: 0x4da6ff, inner: 0x0059b3, type: "rose" },
{ name: "โบตั๋นชมพูพีช", color: 0xff9980, inner: 0xcc3300, type: "rose" },
{ name: "เดซี่ขาวบริสุทธิ์", color: 0xffffff, inner: 0xffcc00, type: "sakura" },
{ name: "ลาเวนเดอร์ม่วง", color: 0xb366ff, inner: 0x5900b3, type: "tulip" },
{ name: "กุหลาบขาวเจ้าหญิง", color: 0xfff8dc, inner: 0xe6d8ad, type: "rose" },
{ name: "คาร์เนชั่นแดง", color: 0xff3333, inner: 0x990000, type: "rose" },
{ name: "ทิวลิปม่วงลึกลับ", color: 0x8a2be2, inner: 0x4b0082, type: "tulip" },
{ name: "กุหลาบน้ำเงินกาแล็กซี", color: 0x1e90ff, inner: 0x00008b, type: "rose" },
{ name: "ซากุระขาวหิมะ", color: 0xffe4e1, inner: 0xffb6c1, type: "sakura" },
{ name: "ทานตะวันแคระทองคำ", color: 0xffc107, inner: 0x4e342e, type: "sunflower" },
{ name: "ไลเซนทัสม่วงอ่อน", color: 0xd8bfd8, inner: 0x800080, type: "rose" },
{ name: "ชบาแดงทรอปิคอล", color: 0xff2400, inner: 0x8b0000, type: "sakura" },
{ name: "คาโมมายล์นวล", color: 0xfffacd, inner: 0xdaa520, type: "sakura" },
{ name: "กล้วยไม้ม่วงหรู", color: 0xda70d6, inner: 0x8b008b, type: "sakura" },
{ name: "กุหลาบพีชละมุน", color: 0xffdab9, inner: 0xff7f50, type: "rose" },
{ name: "ทิวลิปส้มซันเซ็ต", color: 0xff7f50, inner: 0xd2691e, type: "tulip" },
{ name: "พีโอนีชมพูบานเย็น", color: 0xff1493, inner: 0x8b0a50, type: "rose" },
{ name: "คอสมอสพาสเทล", color: 0xf49ac2, inner: 0xc54b8c, type: "sakura" },
{ name: "กุหลาบดำรัตติกาล", color: 0x222222, inner: 0x050505, type: "rose" },
{ name: "สแตติสม่วงพาสเทล", color: 0xc39bd3, inner: 0x6c3483, type: "sakura" },
{ name: "ยิปโซฟิลลาขาว", color: 0xf8f9f9, inner: 0xd5dbdb, type: "sakura" },
{ name: "ทิวลิปเหลืองนวล", color: 0xfff44f, inner: 0xcca300, type: "tulip" },
{ name: "กุหลาบโรสโกลด์", color: 0xe0bfb8, inner: 0xb76e79, type: "rose" },
{ name: "ดอกบัวสวรรค์ชมพู", color: 0xfadbd8, inner: 0xe74c3c, type: "tulip" },
{ name: "ไวโอเล็ตคราม", color: 0x7f00ff, inner: 0x3b0078, type: "sakura" },
{ name: "กุหลาบเรนโบว์", color: 0xff66b2, inner: 0x66b2ff, type: "rose" }
];

const ribbonColors = [
{ name: "โบว์แดงเวลเวท", color: 0xd90429 },
{ name: "โบว์ชมพูพาสเทล", color: 0xff85a1 },
{ name: "โบว์ทองหรูหรา", color: 0xffd700 },
{ name: "โบว์ฟ้าคราม", color: 0x00b4d8 },
{ name: "โบว์ม่วงลาเวนเดอร์", color: 0x9d4edd },
{ name: "โบว์ขาวบริสุทธิ์", color: 0xffffff },
{ name: "โบว์เงินประกาย", color: 0xc0c0c0 },
{ name: "โบว์ส้มพีช", color: 0xff9e00 },
{ name: "โบว์น้ำเงินเข้ม", color: 0x03045e },
{ name: "โบว์บานเย็น", color: 0xf72585 },
{ name: "โบว์เขียวมินต์", color: 0x2ec4b6 },
{ name: "โบว์โรสโกลด์", color: 0xe5989b },
{ name: "โบว์เหลืองนวล", color: 0xffee32 },
{ name: "โบว์ฟ้าพาสเทล", color: 0x90e0ef },
{ name: "โบว์ม่วงเข้ม", color: 0x5a189a },
{ name: "โบว์ดำคลาสสิก", color: 0x1a1a1a },
{ name: "โบว์บรอนซ์", color: 0xcd7f32 },
{ name: "โบว์ชมพูซากุระ", color: 0xffccd5 },
{ name: "โบว์เขียวมรกต", color: 0x008000 },
{ name: "โบว์แดงไวน์", color: 0x6a040f }
];

const wrapStyles = [
{ name: "ชมพูโรแมนติก", color: 0xff758f },
{ name: "ขาวมินิมอลเกาหลี", color: 0xf8f9fa },
{ name: "ดำหรูหราพรีเมียม", color: 0x1f1f1f },
{ name: "ครีมวินเทจ", color: 0xf5ebe0 },
{ name: "ม่วงพาสเทล", color: 0xd8bbff },
{ name: "ฟ้าท้องทะเล", color: 0xa2d2ff },
{ name: "แดงกำมะหยี่", color: 0x9b2226 },
{ name: "พีชซอฟต์", color: 0xffcdb2 },
{ name: "เขียวมัทฉะ", color: 0xb7e4c7 },
{ name: "ทองแชมเปญ", color: 0xffe599 },
{ name: "ชมพูกลีบกุหลาบ", color: 0xffb3c6 },
{ name: "น้ำเงินมิดไนท์", color: 0x1d3557 },
{ name: "ลาเวนเดอร์ดรีม", color: 0xc8b6ff },
{ name: "โรสโกลด์เมทัลลิก", color: 0xe5989b },
{ name: "เขียวมินต์สดใส", color: 0x99e2b4 },
{ name: "ส้มคอรัล", color: 0xf4a261 },
{ name: "เทาโมเดิร์น", color: 0xadb5bd },
{ name: "เหลืองซันไชน์", color: 0xffe66d },
{ name: "ม่วงอเมทิสต์", color: 0x9d4edd },
{ name: "ชมพูคอตตอนแคนดี้", color: 0xffccd5 }
];

const effects = [
"ละอองดาวระยิบระยับ", "หัวใจลอยฟุ้ง", "กลีบซากุระโปรยปราย", "หิมะสีขาวนวล", "ประกายทองคำ"
];

let particleMode = 0;
let randomSeed = 42;
let scene, camera, renderer, controls, bouquetGroup, characterGroup;

function init3D() {
const container = document.getElementById('canvas3d-container');
scene = new THREE.Scene();

camera = new THREE.PerspectiveCamera(42, container.clientWidth / container.clientHeight, 0.1, 1000);
camera.position.set(0, 2.2, 7.8);

renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
renderer.setSize(container.clientWidth, container.clientHeight);
renderer.setPixelRatio(window.devicePixelRatio);
renderer.shadowMap.enabled = true;
container.appendChild(renderer.domElement);

controls = new THREE.OrbitControls(camera, renderer.domElement);
controls.enableDamping = true;
controls.dampingFactor = 0.06;
controls.minDistance = 4;
controls.maxDistance = 12;
controls.autoRotate = true;
controls.autoRotateSpeed = 1.5;

const ambientLight = new THREE.AmbientLight(0xffffff, 0.8);
scene.add(ambientLight);

const dirLight = new THREE.DirectionalLight(0xffffff, 1.05);
dirLight.position.set(5, 10, 7);
scene.add(dirLight);

const rimLight = new THREE.PointLight(0xff758c, 0.9, 20);
rimLight.position.set(-5, 4, -5);
scene.add(rimLight);

bouquetGroup = new THREE.Group();
bouquetGroup.position.set(-0.9, 0, 0);
scene.add(bouquetGroup);

characterGroup = new THREE.Group();
characterGroup.position.set(1.35, -0.35, 0);
scene.add(characterGroup);

rebuild3DBouquet();
rebuild3DCharacter();
animate3D();

window.addEventListener('resize', () => {
if (!container) return;
camera.aspect = container.clientWidth / container.clientHeight;
camera.updateProjectionMatrix();
renderer.setSize(container.clientWidth, container.clientHeight);
});
}

function seededRandom(seed) {
const x = Math.sin(seed) * 10000;
return x - Math.floor(x);
}

function generateFlowerPositions(count, style) {
const positions = [];
const scaleAdjust = count <= 5 ? 1.35 : (count <= 12 ? 1.15 : (count > 26 ? 0.88 : 1.0));

if (style === 'heart') {
for (let i = 0; i < count; i++) {
if (i === 0 && count > 1) {
positions.push({ x: 0, y: 1.42, z: 0.1, rx: 0.1, rz: 0, scale: 1.15 * scaleAdjust });
continue;
}
const ratio = count === 1 ? 0 : i / (count - 1);
const t = ratio * Math.PI * 2;
const layer = (i % 3 === 0) ? 0.55 : 1.0;
const hx = 16 * Math.pow(Math.sin(t), 3);
const hz = -(13 * Math.cos(t) - 5 * Math.cos(2 * t) - 2 * Math.cos(3 * t) - Math.cos(4 * t));
const x = (hx / 16) * 1.25 * layer;
const z = (hz / 16) * 1.15 * layer;
const dist = Math.hypot(x, z);
const y = 1.5 - dist * 0.42;
positions.push({
x, y, z,
rx: z * 0.55,
rz: -x * 0.55,
scale: (1.08 - dist * 0.1) * scaleAdjust
});
}
} else if (style === 'cascade') {
for (let i = 0; i < count; i++) {
const progress = count === 1 ? 0 : i / (count - 1);
const angle = i * 2.39996;
const spread = Math.sqrt(progress) * 1.3;
const x = Math.cos(angle) * spread * 0.9;
const z = (progress * 1.55) - 0.35 + Math.sin(angle) * 0.28;
const y = 1.72 - (progress * 1.05);
positions.push({
x, y, z,
rx: 0.25 + progress * 0.72,
rz: -x * 0.45,
scale: (1.12 - progress * 0.22) * scaleAdjust
});
}
} else if (style === 'minimal') {
for (let i = 0; i < count; i++) {
if (i === 0) {
positions.push({ x: 0, y: 1.62, z: 0, rx: 0, rz: 0, scale: 1.2 * scaleAdjust });
} else {
const ring = Math.floor((i - 1) / 6) + 1;
const angle = ((i - 1) % 6) * (Math.PI / 3) + ring * 0.5;
const radius = Math.min(ring * 0.52, 1.35);
const x = Math.cos(angle) * radius;
const z = Math.sin(angle) * radius;
const y = 1.58 - ring * 0.26;
positions.push({
x, y, z,
rx: Math.sin(angle) * 0.35 * ring,
rz: -Math.cos(angle) * 0.35 * ring,
scale: 1.02 * scaleAdjust
});
}
}
} else if (style === 'random') {
for (let i = 0; i < count; i++) {
const s = randomSeed + i * 17;
const r = Math.sqrt(seededRandom(s)) * 1.32;
const theta = seededRandom(s + 1) * Math.PI * 2;
const x = Math.cos(theta) * r;
const z = Math.sin(theta) * r;
const y = 1.58 - (r * 0.45) + (seededRandom(s + 2) - 0.5) * 0.22;
positions.push({
x, y, z,
rx: z * 0.52 + (seededRandom(s + 3) - 0.5) * 0.25,
rz: -x * 0.52 + (seededRandom(s + 4) - 0.5) * 0.25,
scale: (0.9 + seededRandom(s + 5) * 0.28) * scaleAdjust
});
}
} else {
for (let i = 0; i < count; i++) {
if (i === 0) {
positions.push({ x: 0, y: 1.56, z: 0, rx: 0, rz: 0, scale: 1.15 * scaleAdjust });
continue;
}
const ratio = i / count;
const radius = Math.sqrt(ratio) * 1.32;
const angle = i * 2.39996;
const x = Math.cos(angle) * radius;
const z = Math.sin(angle) * radius;
const y = 1.56 - Math.pow(radius / 1.35, 1.6) * 0.65;
positions.push({
x, y, z,
rx: Math.sin(angle) * radius * 0.58,
rz: -Math.cos(angle) * radius * 0.58,
scale: (1.12 - ratio * 0.14) * scaleAdjust
});
}
}
return positions;
}

function createRealistic3DFlowerHead(fObj) {
const flower = new THREE.Group();
const outerMat = new THREE.MeshStandardMaterial({ color: fObj.color, roughness: 0.45, metalness: 0.08, side: THREE.DoubleSide });
const innerMat = new THREE.MeshStandardMaterial({ color: fObj.inner, roughness: 0.55, metalness: 0.05, side: THREE.DoubleSide });

const calyxGeo = new THREE.ConeGeometry(0.28, 0.35, 8);
const calyxMat = new THREE.MeshStandardMaterial({ color: 0x2d6a4f, roughness: 0.7 });
const calyx = new THREE.Mesh(calyxGeo, calyxMat);
calyx.rotation.x = Math.PI;
calyx.position.y = -0.15;
flower.add(calyx);

if (fObj.type === "sunflower") {
const centerGeo = new THREE.SphereGeometry(0.32, 16, 12);
centerGeo.scale(1, 0.4, 1);
const centerMesh = new THREE.Mesh(centerGeo, innerMat);
centerMesh.position.y = 0.08;
flower.add(centerMesh);

const petalGeo = new THREE.SphereGeometry(0.14, 8, 8);
petalGeo.scale(0.6, 0.15, 2.2);
for (let layer = 0; layer < 2; layer++) {
const count = 14;
for (let i = 0; i < count; i++) {
const angle = (i / count) * Math.PI * 2 + layer * 0.2;
const p = new THREE.Mesh(petalGeo, outerMat);
p.position.set(Math.cos(angle) * 0.42, 0.04 - layer * 0.03, Math.sin(angle) * 0.42);
p.rotation.y = -angle + Math.PI / 2;
p.rotation.x = 0.2;
flower.add(p);
}
}
} else if (fObj.type === "tulip") {
const petalGeo = new THREE.SphereGeometry(0.34, 16, 16, 0, Math.PI * 1.1, 0, Math.PI * 0.75);
for (let i = 0; i < 6; i++) {
const angle = (i / 6) * Math.PI * 2;
const p = new THREE.Mesh(petalGeo, i % 2 === 0 ? outerMat : innerMat);
p.position.set(Math.cos(angle) * 0.12, 0.22, Math.sin(angle) * 0.12);
p.rotation.y = -angle;
p.rotation.z = -0.18;
flower.add(p);
}
} else if (fObj.type === "sakura") {
const petalGeo = new THREE.SphereGeometry(0.24, 12, 12);
petalGeo.scale(0.85, 0.18, 1.4);
for (let i = 0; i < 6; i++) {
const angle = (i / 6) * Math.PI * 2;
const p = new THREE.Mesh(petalGeo, outerMat);
p.position.set(Math.cos(angle) * 0.28, 0.08, Math.sin(angle) * 0.28);
p.rotation.y = -angle + Math.PI / 2;
p.rotation.x = 0.35;
flower.add(p);
}
const stamen = new THREE.Mesh(new THREE.SphereGeometry(0.14, 12, 12), innerMat);
stamen.position.y = 0.14;
flower.add(stamen);
} else {
const bud = new THREE.Mesh(new THREE.SphereGeometry(0.22, 16, 16), innerMat);
bud.position.y = 0.22;
flower.add(bud);

const innerPetalGeo = new THREE.SphereGeometry(0.28, 14, 14, 0, Math.PI * 1.2, 0, Math.PI * 0.7);
for (let i = 0; i < 5; i++) {
const angle = (i / 5) * Math.PI * 2;
const p = new THREE.Mesh(innerPetalGeo, innerMat);
p.position.set(Math.cos(angle) * 0.14, 0.18, Math.sin(angle) * 0.14);
p.rotation.y = -angle;
p.rotation.z = -0.35;
flower.add(p);
}

const midPetalGeo = new THREE.SphereGeometry(0.36, 16, 16, 0, Math.PI * 1.25, 0, Math.PI * 0.72);
for (let i = 0; i < 7; i++) {
const angle = (i / 7) * Math.PI * 2 + 0.4;
const p = new THREE.Mesh(midPetalGeo, outerMat);
p.position.set(Math.cos(angle) * 0.22, 0.13, Math.sin(angle) * 0.22);
p.rotation.y = -angle;
p.rotation.z = -0.58;
flower.add(p);
}

const outerPetalGeo = new THREE.SphereGeometry(0.42, 16, 16, 0, Math.PI * 1.3, 0, Math.PI * 0.75);
for (let i = 0; i < 8; i++) {
const angle = (i / 8) * Math.PI * 2;
const p = new THREE.Mesh(outerPetalGeo, outerMat);
p.position.set(Math.cos(angle) * 0.28, 0.06, Math.sin(angle) * 0.28);
p.rotation.y = -angle;
p.rotation.z = -0.82;
flower.add(p);
}
}
return flower;
}

function rebuild3DBouquet() {
if (!bouquetGroup) return;
while (bouquetGroup.children.length > 0) {
bouquetGroup.remove(bouquetGroup.children[0]);
}

const fIdx = document.getElementById('flowerSelect').value || 0;
const rIdx = document.getElementById('ribbonSelect').value || 0;
const wIdx = document.getElementById('bouquetSelect').value || 0;
const flowerCount = parseInt(document.getElementById('flowerCountSlider').value) || 19;
const arrangeStyle = document.getElementById('arrangementSelect').value || 'dome';

const fObj = flowerData[fIdx];
const rObj = ribbonColors[rIdx];
const wObj = wrapStyles[wIdx];

const positions = generateFlowerPositions(flowerCount, arrangeStyle);
const stemMat = new THREE.MeshStandardMaterial({ color: 0x2d6a4f, roughness: 0.7 });

positions.forEach(pos => {
const head = createRealistic3DFlowerHead(fObj);
head.position.set(pos.x, pos.y, pos.z);
head.rotation.set(pos.rx, 0, pos.rz);
head.scale.setScalar(pos.scale);
bouquetGroup.add(head);

const stemStart = new THREE.Vector3(pos.x * 0.85, pos.y - 0.15, pos.z * 0.85);
const stemEnd = new THREE.Vector3(0, -0.7, 0);
const stemCurve = new THREE.LineCurve3(stemStart, stemEnd);
const stemMesh = new THREE.Mesh(new THREE.TubeGeometry(stemCurve, 4, 0.032, 8, false), stemMat);
bouquetGroup.add(stemMesh);
});

const leafGeo = new THREE.SphereGeometry(0.35, 10, 8);
leafGeo.scale(0.5, 0.08, 1.6);
const leafMat = new THREE.MeshStandardMaterial({ color: 0x1b4332, roughness: 0.6 });
const leafCount = Math.max(8, Math.min(18, Math.round(flowerCount * 0.85)));
for (let i = 0; i < leafCount; i++) {
const a = (i / leafCount) * Math.PI * 2;
const leaf = new THREE.Mesh(leafGeo, leafMat);
leaf.position.set(Math.cos(a) * 1.48, 0.68, Math.sin(a) * 1.48);
leaf.rotation.y = -a + Math.PI / 2;
leaf.rotation.x = 0.65;
bouquetGroup.add(leaf);
}

const wrapMat = new THREE.MeshStandardMaterial({ color: wObj.color, roughness: 0.6, metalness: 0.1, side: THREE.DoubleSide });
const topCone = new THREE.Mesh(new THREE.ConeGeometry(1.78, 2.2, 32, 1, true), wrapMat);
topCone.rotation.x = Math.PI;
topCone.position.y = -0.25;
bouquetGroup.add(topCone);

const bottomCone = new THREE.Mesh(new THREE.ConeGeometry(0.65, 1.5, 32), wrapMat);
bottomCone.position.y = -1.45;
bouquetGroup.add(bottomCone);

const ribbonMat = new THREE.MeshStandardMaterial({ color: rObj.color, roughness: 0.3, metalness: 0.25 });
const band = new THREE.Mesh(new THREE.TorusGeometry(0.56, 0.09, 12, 32), ribbonMat);
band.rotation.x = Math.PI / 2;
band.position.y = -0.95;
bouquetGroup.add(band);

const knot = new THREE.Mesh(new THREE.SphereGeometry(0.14, 12, 12), ribbonMat);
knot.position.set(0, -0.95, 0.62);
bouquetGroup.add(knot);

const loopGeo = new THREE.TorusGeometry(0.22, 0.06, 10, 24);
loopGeo.scale(1.3, 0.7, 0.5);
const leftLoop = new THREE.Mesh(loopGeo, ribbonMat);
leftLoop.position.set(-0.24, -0.92, 0.62);
leftLoop.rotation.z = 0.3;
bouquetGroup.add(leftLoop);

const rightLoop = new THREE.Mesh(loopGeo, ribbonMat);
rightLoop.position.set(0.24, -0.92, 0.62);
rightLoop.rotation.z = -0.3;
bouquetGroup.add(rightLoop);

const tailGeo = new THREE.BoxGeometry(0.11, 0.65, 0.03);
const leftTail = new THREE.Mesh(tailGeo, ribbonMat);
leftTail.position.set(-0.15, -1.28, 0.6);
leftTail.rotation.z = -0.22;
bouquetGroup.add(leftTail);

const rightTail = new THREE.Mesh(tailGeo, ribbonMat);
rightTail.position.set(0.15, -1.28, 0.6);
rightTail.rotation.z = 0.22;
bouquetGroup.add(rightTail);
}

function updateFlowerCount() {
const val = document.getElementById('flowerCountSlider').value;
document.getElementById('flowerCountLabel').innerText = `${val} ดอก`;
rebuild3DBouquet();
}

function randomizeArrangement() {
document.getElementById('arrangementSelect').value = 'random';
randomSeed = Math.floor(Math.random() * 10000);
rebuild3DBouquet();
showToast("🎲 สุ่มจัดตำแหน่งช่อดอกไม้ใหม่แล้ว!");
}

function addCuteEmojiFace(group, headY, headRadiusZ, eyeSpread = 0.17, eyeYOffset = 0.04, isRobot = false) {
const eyeMat = new THREE.MeshStandardMaterial({ 
color: isRobot ? 0x00f5d4 : 0x1a1412, 
roughness: 0.1, 
metalness: 0.1,
emissive: isRobot ? 0x00bbf9 : 0x000000,
emissiveIntensity: isRobot ? 0.6 : 0
});
const shineMat = new THREE.MeshBasicMaterial({ color: 0xffffff });
const blushMat = new THREE.MeshStandardMaterial({ color: 0xff8fab, roughness: 0.8 });

const eyeGeo = new THREE.SphereGeometry(0.065, 24, 24);
eyeGeo.scale(0.95, 1.15, 0.6);
const eyeL = new THREE.Mesh(eyeGeo, eyeMat);
eyeL.position.set(-eyeSpread, headY + eyeYOffset, headRadiusZ - 0.02);
const eyeR = new THREE.Mesh(eyeGeo, eyeMat);
eyeR.position.set(eyeSpread, headY + eyeYOffset, headRadiusZ - 0.02);
group.add(eyeL, eyeR);

const shineGeo = new THREE.SphereGeometry(0.022, 16, 16);
const shineL = new THREE.Mesh(shineGeo, shineMat);
shineL.position.set(-eyeSpread - 0.02, headY + eyeYOffset + 0.025, headRadiusZ + 0.015);
const shineR = new THREE.Mesh(shineGeo, shineMat);
shineR.position.set(eyeSpread - 0.02, headY + eyeYOffset + 0.025, headRadiusZ + 0.015);
group.add(shineL, shineR);

const blushGeo = new THREE.SphereGeometry(0.075, 20, 20);
blushGeo.scale(1.3, 0.75, 0.4);
const blushL = new THREE.Mesh(blushGeo, blushMat);
blushL.position.set(-eyeSpread - 0.08, headY - 0.06, headRadiusZ - 0.05);
blushL.rotation.y = -0.35;
const blushR = new THREE.Mesh(blushGeo, blushMat);
blushR.position.set(eyeSpread + 0.08, headY - 0.06, headRadiusZ - 0.05);
blushR.rotation.y = 0.35;
group.add(blushL, blushR);
}

function addPlushBodyLimbs(group, bodyMat, bellyMat, pawMat) {
const bodyGeo = new THREE.SphereGeometry(0.46, 36, 36);
bodyGeo.scale(1.02, 1.18, 0.95);
const body = new THREE.Mesh(bodyGeo, bodyMat);
body.position.set(0, -0.42, 0);
group.add(body);

if (bellyMat) {
const bellyGeo = new THREE.SphereGeometry(0.32, 32, 32);
bellyGeo.scale(0.95, 1.15, 0.45);
const belly = new THREE.Mesh(bellyGeo, bellyMat);
belly.position.set(0, -0.44, 0.32);
group.add(belly);
}

const armGeo = new THREE.SphereGeometry(0.16, 28, 28);
armGeo.scale(0.85, 1.65, 0.85);
const armL = new THREE.Mesh(armGeo, bodyMat);
armL.position.set(-0.46, -0.34, 0.08);
armL.rotation.set(-0.25, 0, 0.55);

const armR = new THREE.Mesh(armGeo, bodyMat);
armR.position.set(0.46, -0.34, 0.08);
armR.rotation.set(-0.25, 0, -0.55);
group.add(armL, armR);

const legGeo = new THREE.SphereGeometry(0.19, 28, 28);
legGeo.scale(0.95, 1.25, 1.15);
const legL = new THREE.Mesh(legGeo, bodyMat);
legL.position.set(-0.23, -0.88, 0.14);
const legR = new THREE.Mesh(legGeo, bodyMat);
legR.position.set(0.23, -0.88, 0.14);
group.add(legL, legR);

if (pawMat) {
const pawGeo = new THREE.SphereGeometry(0.11, 20, 20);
pawGeo.scale(1, 1, 0.4);
const pawL = new THREE.Mesh(pawGeo, pawMat);
pawL.position.set(-0.23, -0.9, 0.34);
const pawR = new THREE.Mesh(pawGeo, pawMat);
pawR.position.set(0.23, -0.9, 0.34);
group.add(pawL, pawR);
}
}

function rebuild3DCharacter() {
if (!characterGroup) return;
while (characterGroup.children.length > 0) {
characterGroup.remove(characterGroup.children[0]);
}

const type = parseInt(document.getElementById('characterSelect').value) || 0;
const charGroup = new THREE.Group();

if (type === 0) {
const furMat = new THREE.MeshStandardMaterial({ color: 0x9c5a2b, roughness: 0.65, metalness: 0.05 });
const muzzleMat = new THREE.MeshStandardMaterial({ color: 0xf3d5b5, roughness: 0.6 });
const noseMat = new THREE.MeshStandardMaterial({ color: 0x3d2214, roughness: 0.25 });

addPlushBodyLimbs(charGroup, furMat, muzzleMat, muzzleMat);

const headGeo = new THREE.SphereGeometry(0.5, 40, 40);
headGeo.scale(1.08, 0.96, 0.98);
const head = new THREE.Mesh(headGeo, furMat);
head.position.set(0, 0.36, 0);
charGroup.add(head);

const earOuterGeo = new THREE.SphereGeometry(0.18, 28, 28);
earOuterGeo.scale(1, 1, 0.65);
const earInnerGeo = new THREE.SphereGeometry(0.11, 24, 24);
earInnerGeo.scale(1, 1, 0.65);

[-0.4, 0.4].forEach(xPos => {
const ear = new THREE.Mesh(earOuterGeo, furMat);
ear.position.set(xPos, 0.76, -0.02);
const inner = new THREE.Mesh(earInnerGeo, muzzleMat);
inner.position.set(xPos, 0.76, 0.04);
charGroup.add(ear, inner);
});

const snoutGeo = new THREE.SphereGeometry(0.17, 28, 28);
snoutGeo.scale(1.2, 0.9, 0.75);
const snout = new THREE.Mesh(snoutGeo, muzzleMat);
snout.position.set(0, 0.25, 0.44);

const noseGeo = new THREE.SphereGeometry(0.065, 24, 24);
noseGeo.scale(1.3, 0.85, 0.8);
const nose = new THREE.Mesh(noseGeo, noseMat);
nose.position.set(0, 0.3, 0.55);
charGroup.add(snout, nose);

const tail = new THREE.Mesh(new THREE.SphereGeometry(0.14, 20, 20), furMat);
tail.position.set(0, -0.72, -0.44);
charGroup.add(tail);

addCuteEmojiFace(charGroup, 0.38, 0.48, 0.18, 0.04);

} else if (type === 1) {
const catMat = new THREE.MeshStandardMaterial({ color: 0xffd166, roughness: 0.55 });
const creamMat = new THREE.MeshStandardMaterial({ color: 0xfff8e7, roughness: 0.6 });
const pinkMat = new THREE.MeshStandardMaterial({ color: 0xff8fab, roughness: 0.5 });
const stripeMat = new THREE.MeshStandardMaterial({ color: 0xe09f3e, roughness: 0.6 });

addPlushBodyLimbs(charGroup, catMat, creamMat, pinkMat);

const headGeo = new THREE.SphereGeometry(0.49, 40, 40);
headGeo.scale(1.12, 0.94, 0.96);
const head = new THREE.Mesh(headGeo, catMat);
head.position.set(0, 0.36, 0);
charGroup.add(head);

const earGeo = new THREE.SphereGeometry(0.19, 28, 28);
earGeo.scale(0.85, 1.35, 0.5);
const earInGeo = new THREE.SphereGeometry(0.11, 20, 20);
earInGeo.scale(0.8, 1.25, 0.5);

[-0.32, 0.32].forEach((xPos, idx) => {
const rotZ = idx === 0 ? 0.32 : -0.32;
const ear = new THREE.Mesh(earGeo, catMat);
ear.position.set(xPos, 0.78, 0);
ear.rotation.z = rotZ;

const inner = new THREE.Mesh(earInGeo, pinkMat);
inner.position.set(xPos * 0.96, 0.76, 0.05);
inner.rotation.z = rotZ;
charGroup.add(ear, inner);
});

[-0.08, 0, 0.08].forEach((xPos, idx) => {
const stripe = new THREE.Mesh(new THREE.SphereGeometry(0.03, 16, 16), stripeMat);
stripe.scale.set(0.6, idx === 1 ? 2.2 : 1.6, 0.5);
stripe.position.set(xPos, 0.72, 0.34);
stripe.rotation.x = -0.5;
charGroup.add(stripe);
});

const nose = new THREE.Mesh(new THREE.SphereGeometry(0.045, 20, 20), pinkMat);
nose.scale.set(1.3, 0.85, 0.8);
nose.position.set(0, 0.28, 0.47);
charGroup.add(nose);

const tailCurve = new THREE.CatmullRomCurve3([
new THREE.Vector3(0, -0.7, -0.38),
new THREE.Vector3(0.15, -0.55, -0.65),
new THREE.Vector3(0.25, -0.25, -0.68),
new THREE.Vector3(0.18, -0.05, -0.55)
]);
const tail = new THREE.Mesh(new THREE.TubeGeometry(tailCurve, 24, 0.075, 16, false), catMat);
charGroup.add(tail);

addCuteEmojiFace(charGroup, 0.36, 0.47, 0.18, 0.03);

} else if (type === 2) {
const bunnyMat = new THREE.MeshStandardMaterial({ color: 0xffffff, roughness: 0.55 });
const pinkMat = new THREE.MeshStandardMaterial({ color: 0xffafcc, roughness: 0.5 });

addPlushBodyLimbs(charGroup, bunnyMat, pinkMat, pinkMat);

const headGeo = new THREE.SphereGeometry(0.48, 40, 40);
headGeo.scale(1.06, 0.98, 0.96);
const head = new THREE.Mesh(headGeo, bunnyMat);
head.position.set(0, 0.36, 0);
charGroup.add(head);

const earGeo = new THREE.SphereGeometry(0.13, 28, 28);
earGeo.scale(0.85, 3.2, 0.45);
const earInGeo = new THREE.SphereGeometry(0.08, 24, 24);
earInGeo.scale(0.8, 2.9, 0.45);

[-0.19, 0.19].forEach((xPos, idx) => {
const rotZ = idx === 0 ? 0.12 : -0.18;
const ear = new THREE.Mesh(earGeo, bunnyMat);
ear.position.set(xPos, 1.05, -0.02);
ear.rotation.z = rotZ;

const inner = new THREE.Mesh(earInGeo, pinkMat);
inner.position.set(xPos, 1.03, 0.03);
inner.rotation.z = rotZ;
charGroup.add(ear, inner);
});

const nose = new THREE.Mesh(new THREE.SphereGeometry(0.048, 20, 20), pinkMat);
nose.scale.set(1.25, 0.9, 0.8);
nose.position.set(0, 0.28, 0.46);
charGroup.add(nose);

const tail = new THREE.Mesh(new THREE.SphereGeometry(0.17, 24, 24), bunnyMat);
tail.position.set(0, -0.72, -0.45);
charGroup.add(tail);

addCuteEmojiFace(charGroup, 0.36, 0.46, 0.17, 0.03);

} else if (type === 3) {
const bodyMat = new THREE.MeshStandardMaterial({ color: 0xf8f9fa, roughness: 0.25, metalness: 0.15 });
const accentMat = new THREE.MeshStandardMaterial({ color: 0x4cc9f0, roughness: 0.3, metalness: 0.3 });
const screenMat = new THREE.MeshStandardMaterial({ color: 0x101828, roughness: 0.1, metalness: 0.5 });

addPlushBodyLimbs(charGroup, bodyMat, accentMat, accentMat);

const headGeo = new THREE.SphereGeometry(0.5, 40, 40);
headGeo.scale(1.15, 0.92, 0.95);
const head = new THREE.Mesh(headGeo, bodyMat);
head.position.set(0, 0.38, 0);
charGroup.add(head);

const visorGeo = new THREE.SphereGeometry(0.44, 36, 36);
visorGeo.scale(1.08, 0.72, 0.65);
const visor = new THREE.Mesh(visorGeo, screenMat);
visor.position.set(0, 0.38, 0.24);
charGroup.add(visor);

const earGeo = new THREE.SphereGeometry(0.16, 24, 24);
earGeo.scale(0.5, 1.1, 1.1);
const earL = new THREE.Mesh(earGeo, accentMat);
earL.position.set(-0.56, 0.38, 0);
const earR = new THREE.Mesh(earGeo, accentMat);
earR.position.set(0.56, 0.38, 0);
charGroup.add(earL, earR);

const stem = new THREE.Mesh(new THREE.CylinderGeometry(0.025, 0.035, 0.24, 16), accentMat);
stem.position.set(0, 0.92, 0);
const bulb = new THREE.Mesh(new THREE.SphereGeometry(0.08, 24, 24), new THREE.MeshStandardMaterial({ 
color: 0xff4d6d, emissive: 0xff4d6d, emissiveIntensity: 0.5 
}));
bulb.position.set(0, 1.06, 0);
charGroup.add(stem, bulb);

addCuteEmojiFace(charGroup, 0.38, 0.51, 0.18, 0.02, true);

} else {
const uniMat = new THREE.MeshStandardMaterial({ color: 0xffffff, roughness: 0.5 });
const maneMat1 = new THREE.MeshStandardMaterial({ color: 0xff70a6, roughness: 0.45 });
const maneMat2 = new THREE.MeshStandardMaterial({ color: 0x70d6ff, roughness: 0.45 });
const maneMat3 = new THREE.MeshStandardMaterial({ color: 0xc77dff, roughness: 0.45 });
const hornMat = new THREE.MeshStandardMaterial({ color: 0xffd166, metalness: 0.6, roughness: 0.2 });

addPlushBodyLimbs(charGroup, uniMat, maneMat1, maneMat3);

const headGeo = new THREE.SphereGeometry(0.48, 40, 40);
headGeo.scale(1.05, 0.98, 1.04);
const head = new THREE.Mesh(headGeo, uniMat);
head.position.set(0, 0.36, 0);
charGroup.add(head);

const muzzle = new THREE.Mesh(new THREE.SphereGeometry(0.22, 28, 28), new THREE.MeshStandardMaterial({ color: 0xffe5ec, roughness: 0.55 }));
muzzle.scale.set(1.15, 0.8, 0.85);
muzzle.position.set(0, 0.24, 0.4);
charGroup.add(muzzle);

for (let i = 0; i < 5; i++) {
const radius = 0.085 - i * 0.013;
const ring = new THREE.Mesh(new THREE.SphereGeometry(radius, 20, 20), hornMat);
ring.scale.set(1, 0.75, 1);
ring.position.set(0, 0.84 + i * 0.075, 0.14 + i * 0.015);
charGroup.add(ring);
}

const earGeo = new THREE.SphereGeometry(0.13, 24, 24);
earGeo.scale(0.8, 1.4, 0.5);
const earL = new THREE.Mesh(earGeo, uniMat);
earL.position.set(-0.28, 0.78, -0.02);
earL.rotation.z = 0.3;
const earR = new THREE.Mesh(earGeo, uniMat);
earR.position.set(0.28, 0.78, -0.02);
earR.rotation.z = -0.3;
charGroup.add(earL, earR);

const manePositions = [
{ x: -0.16, y: 0.82, z: 0.18, mat: maneMat1, s: 0.13 },
{ x: 0.14, y: 0.80, z: 0.20, mat: maneMat2, s: 0.12 },
{ x: -0.26, y: 0.68, z: -0.22, mat: maneMat3, s: 0.16 },
{ x: -0.32, y: 0.45, z: -0.34, mat: maneMat1, s: 0.17 },
{ x: -0.28, y: 0.20, z: -0.38, mat: maneMat2, s: 0.16 },
{ x: -0.22, y: -0.05, z: -0.36, mat: maneMat3, s: 0.15 }
];
manePositions.forEach(m => {
const puff = new THREE.Mesh(new THREE.SphereGeometry(m.s, 20, 20), m.mat);
puff.position.set(m.x, m.y, m.z);
charGroup.add(puff);
});

const tail1 = new THREE.Mesh(new THREE.SphereGeometry(0.18, 20, 20), maneMat1);
tail1.position.set(0, -0.68, -0.45);
const tail2 = new THREE.Mesh(new THREE.SphereGeometry(0.15, 20, 20), maneMat2);
tail2.position.set(0.06, -0.82, -0.52);
charGroup.add(tail1, tail2);

addCuteEmojiFace(charGroup, 0.36, 0.48, 0.18, 0.03);
}

characterGroup.add(charGroup);
}

function animate3D() {
requestAnimationFrame(animate3D);
if (controls) controls.update();
if (renderer && scene && camera) {
renderer.render(scene, camera);

if (characterGroup) {
const vector = new THREE.Vector3(1.35, 1.35, 0);
vector.project(camera);

const container = document.getElementById('canvas3d-container');
const width = container.clientWidth;
const height = container.clientHeight;

const x = (vector.x * .5 + .5) * width;
const y = (vector.y * -.5 + .5) * height;

const bubble = document.getElementById('speechBubble');
bubble.style.left = `${x}px`;
bubble.style.top = `${y}px`;
}
}
}

window.onload = function() {
populateSelect('flowerSelect', flowerData.map(f => f.name));
populateSelect('ribbonSelect', ribbonColors.map(r => r.name));
populateSelect('bouquetSelect', wrapStyles.map(w => w.name));
populateSelect('effectSelect', effects);
init3D();
};

function populateSelect(id, list) {
const el = document.getElementById(id);
el.innerHTML = '';
list.forEach((item, idx) => {
const opt = document.createElement('option');
opt.value = idx;
opt.text = `${idx + 1}. ${item}`;
el.appendChild(opt);
});
}

function updateSpeechText() {
const text = document.getElementById('characterMessageInput').value;
document.getElementById('speechBubble').innerText = text || "...";
}

function updateEffect() {
particleMode = parseInt(document.getElementById('effectSelect').value) || 0;
}

function toggleEnvelope() {
document.getElementById('envelopeCard').classList.toggle('open');
}

function triggerUpload(num) {
document.getElementById('up' + num).click();
}

function loadPhoto(event, num) {
if (!event.target.files[0]) return;
const reader = new FileReader();
reader.onload = function() {
const slots = document.getElementsByClassName('photo-slot');
slots[num - 1].innerHTML = `<img src="${reader.result}" alt="Memory ${num}">`;
};
reader.readAsDataURL(event.target.files[0]);
}

// ฟังก์ชันแปลงลิงก์ YouTube เป็น Video ID
function extractYouTubeId(url) {
const regExp = /^.*(youtu.be\/|v\/|u\/\w\/|embed\/|watch\?v=|\&v=)([^#\&\?]*).*/;
const match = url.match(regExp);
return (match && match[2].length === 11) ? match[2] : null;
}

// ฟังก์ชันเล่นเพลงจาก YouTube
function playMusic() {
const url = document.getElementById('musicUrl').value.trim();
const playerWrapper = document.getElementById('youtubePlayerWrapper');

if (!url) {
showToast("⚠️ กรุณาวางลิงก์ YouTube ก่อนครับ");
return;
}

const videoId = extractYouTubeId(url);
if (videoId) {
playerWrapper.innerHTML = `<iframe width="100%" height="80" src="https://www.youtube.com/embed/${videoId}?autoplay=1&loop=1&playlist=${videoId}" title="YouTube music player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" style="border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.3);"></iframe>`;
showToast("🎵 กำลังเล่นเพลงจาก YouTube");
} else {
showToast("⚠️ ลิงก์ YouTube ไม่ถูกต้อง กรุณาตรวจสอบอีกครั้ง");
}
}

function saveAndCopy() {
const randomCode = Math.random().toString(36).substring(2, 8);
const shareUrl = window.location.origin + window.location.pathname + "?gift=" + randomCode;
if (navigator.clipboard) {
navigator.clipboard.writeText(shareUrl).catch(() => {});
}
showToast("✅ เซฟแล้ว! คัดลอกลิงก์เรียบร้อย");
}

function showToast(msg) {
const toast = document.getElementById('toastNotification');
toast.innerText = msg;
toast.classList.add('show');
clearTimeout(window.toastTimer);
window.toastTimer = setTimeout(() => {
toast.classList.remove('show');
}, 3000);
}

const canvas = document.getElementById('particles');
const ctx = canvas.getContext('2d');
canvas.width = window.innerWidth;
canvas.height = window.innerHeight;

const particles = Array.from({ length: 55 }, () => ({
x: Math.random() * canvas.width,
y: Math.random() * canvas.height,
size: Math.random() * 4 + 2,
speedY: Math.random() * 0.6 + 0.2,
opacity: Math.random() * 0.7 + 0.2
}));

function animateParticles() {
ctx.clearRect(0, 0, canvas.width, canvas.height);
particles.forEach(p => {
ctx.globalAlpha = p.opacity;
if (particleMode === 1) {
ctx.font = `${p.size * 3}px serif`;
ctx.fillText("❤️", p.x, p.y);
} else if (particleMode === 2) {
ctx.font = `${p.size * 3}px serif`;
ctx.fillText("🌸", p.x, p.y);
} else {
ctx.fillStyle = particleMode === 4 ? "#ffd700" : "#ffffff";
ctx.beginPath();
ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
ctx.fill();
}
p.y -= p.speedY;
if (p.y < -10) {
p.y = canvas.height + 10;
p.x = Math.random() * canvas.width;
}
});
requestAnimationFrame(animateParticles);
}
animateParticles();

window.addEventListener('resize', () => {
canvas.width = window.innerWidth;
canvas.height = window.innerHeight;
});
</script>

</body>
</html>

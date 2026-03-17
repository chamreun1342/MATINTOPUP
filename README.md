<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ហាងអនឡាញ - បង់ប្រាក់អូតូ</title>
    <style>
        body { font-family: 'Khmer OS Battambang', sans-serif; background: #f4f4f4; margin: 0; padding-bottom: 280px; }
        .header { background: #0088cc; color: white; text-align: center; padding: 15px; position: sticky; top: 0; z-index: 100; }
        .container { max-width: 600px; margin: auto; padding: 10px; }
        .product-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 10px; }
        .product-card { background: white; border-radius: 8px; padding: 10px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.1); border: 1px solid #ddd; }
        .product-card img { width: 100%; height: 140px; object-fit: cover; border-radius: 5px; }
        .product-card h4 { margin: 8px 0 5px; font-size: 13px; height: 35px; overflow: hidden; color: #333; }
        .product-card .price { color: #d9534f; font-weight: bold; margin-bottom: 8px; display: block; }
        .btn-group { display: flex; gap: 4px; }
        .btn-add { background: #ff9900; color: white; border: none; padding: 8px; border-radius: 4px; flex: 1; cursor: pointer; font-size: 11px; font-weight: bold; }
        .btn-buy { background: #0088cc; color: white; border: none; padding: 8px; border-radius: 4px; flex: 1; cursor: pointer; font-size: 11px; font-weight: bold; }
        
        .order-panel { position: fixed; bottom: 0; left: 0; right: 0; background: white; padding: 15px; box-shadow: 0 -5px 15px rgba(0,0,0,0.2); z-index: 1000; max-width: 600px; margin: auto; border-radius: 15px 15px 0 0; }
        .summary { display: flex; justify-content: space-between; margin-bottom: 10px; font-weight: bold; color: #333; }
        .input-box { display: flex; flex-direction: column; gap: 8px; }
        input, textarea { width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 6px; box-sizing: border-box; font-family: inherit; }
        .btn-submit { background: #28a745; color: white; border: none; padding: 12px; border-radius: 6px; font-size: 16px; font-weight: bold; cursor: pointer; }

        /* Modal */
        #paymentModal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); z-index: 2000; justify-content: center; align-items: center; }
        .modal-content { background: white; padding: 20px; border-radius: 15px; text-align: center; max-width: 300px; width: 90%; }
        .qr-img { width: 100%; border-radius: 10px; margin-bottom: 15px; cursor: pointer; border: 3px solid #0088cc; animation: pulse 2s infinite; }
        @keyframes pulse { 0% { box-shadow: 0 0 0 0 rgba(0, 136, 204, 0.7); } 70% { box-shadow: 0 0 0 10px rgba(0, 136, 204, 0); } 100% { box-shadow: 0 0 0 0 rgba(0, 136, 204, 0); } }
        .btn-confirm { background: #28a745; color: white; border: none; padding: 12px; width: 100%; border-radius: 8px; font-weight: bold; cursor: pointer; }
    </style>
</head>
<body>

<div class="header"><h2>🏪 ហាងកុម្ម៉ង់រហ័ស</h2></div>

<div class="container">
    <div class="product-grid" id="productDisplay"></div>
</div>

<div class="order-panel">
    <div class="summary">
        <span id="cartCount">🛒 0 មុខ</span>
        <span id="totalDisplay" style="color:#d9534f; font-size: 18px;">សរុប: $0.00</span>
    </div>
    <div class="input-box">
        <input type="text" id="custName" placeholder="👤 ឈ្មោះរបស់អ្នក">
        <input type="text" id="phone" placeholder="📞 លេខទូរសព្ទ">
        <textarea id="address" rows="2" placeholder="📍 អាសយដ្ឋានដឹកជញ្ជូន"></textarea>
        <button class="btn-submit" onclick="openPayment()">📤 បញ្ជាទិញឥឡូវនេះ</button>
    </div>
</div>

<div id="paymentModal">
    <div class="modal-content">
        <h3 style="color:#0088cc;">បង់ប្រាក់តាម ABA</h3>
        <p style="font-size: 18px; font-weight: bold; color: red;">ត្រូវបង់: <span id="amountShow">$0.00</span></p>
        <p style="font-size: 12px; color: #666; margin-bottom: 10px;">👇 ចុចលើរូប QR ដើម្បីបង់ប្រាក់អូតូ</p>
        
        <a id="abaLink" href="https://pay.ababank.com/oRF8/quqnct3c" target="_blank">
            <img src="aba_mobile.jpg" class="qr-img" alt="Click to Pay">
        </a>

        <button class="btn-confirm" onclick="finishOrder()">✅ ខ្ញុំបានបង់ប្រាក់រួចរាល់</button>
        <p onclick="document.getElementById('paymentModal').style.display='none'" style="margin-top:15px; color:red; cursor:pointer; font-size: 14px;">បោះបង់</p>
    </div>
</div>

<script>
    const allProducts = [
        { id: 1, name: "អាវយឺតដៃខ្លី ម៉ូតថ្មី", price: "5.50", img: "aba_mobile.jpg" },
        { id: 2, name: "ខោខូវប៊យ បុរស", price: "12.00", img: "IMG_20260317_032745_060.jpg" },
        { id: 3, name: "កាបូបស្ពាយ ពណ៌ខ្មៅ", price: "18.00", img: "aba_mobile.jpg" }
    ];

    let cart = [];

    function renderProducts() {
        const grid = document.getElementById('productDisplay');
        allProducts.forEach(item => {
            grid.innerHTML += `
                <div class="product-card">
                    <img src="${item.img}">
                    <h4>${item.name}</h4>
                    <span class="price">$${item.price}</span>
                    <div class="btn-group">
                        <button class="btn-add" onclick="addToCart('${item.name}', ${item.price})">Add</button>
                        <button class="btn-buy" onclick="buyNow('${item.name}', ${item.price})">Buy</button>
                    </div>
                </div>`;
        });
    }

    function addToCart(name, price) { cart.push({ name, price }); updateUI(); }
    function buyNow(name, price) { cart = [{ name, price }]; updateUI(); document.getElementById('custName').focus(); }

    function updateUI() {
        let total = cart.reduce((sum, item) => sum + parseFloat(item.price), 0);
        document.getElementById('cartCount').innerText = `🛒 ${cart.length} មុខ`;
        document.getElementById('totalDisplay').innerText = `សរុប: $${total.toFixed(2)}`;
    }

    // --- កន្លែងកំណត់ Link ឱ្យលោតលុយអូតូ ---
    function openPayment() {
        const name = document.getElementById('custName').value;
        const phone = document.getElementById('phone').value;
        const address = document.getElementById('address').value;

        if (cart.length === 0 || !name || !phone || !address) {
            alert("សូមបំពេញព័ត៌មានឱ្យគ្រប់!"); return;
        }

        let total = cart.reduce((sum, item) => sum + parseFloat(item.price), 0).toFixed(2);
        
        // បង្ហាញតម្លៃលើ Modal
        document.getElementById('amountShow').innerText = "$" + total;

        // បង្កើត Link ABA ឱ្យលោតតម្លៃលុយអូតូក្នុង App ភ្ញៀវ
        // ប្រើ ID ដែលអ្នកបានផ្ញើមក៖ oRF8/quqnct3c
        const abaId = "oRF8/quqnct3c";
        const payLink = `https://link.payway.com.kh/aba?id=${abaId}&amount=${total}`;

        document.getElementById('abaLink').href = payLink;
        document.getElementById('paymentModal').style.display = 'flex';
    }

    function finishOrder() {
        document.getElementById('paymentModal').style.display = 'none';
        
        const token = "8502623825:AAE9MFP9sQXkqEBdHeQ9oZnp9TxU6g5mL3Y"; 
        const chat_id = "1643504321";
        const name = document.getElementById('custName').value;
        const phone = document.getElementById('phone').value;
        const address = document.getElementById('address').value;
        let totalVal = cart.reduce((sum, i) => sum + parseFloat(i.price), 0).toFixed(2);

        const message = `✅ *ការទូទាត់ជោគជ័យ*%0A👤 ឈ្មោះ: ${name}%0A📞 លេខ: ${phone}%0A📍 ទីតាំង: ${address}%0A💰 ចំនួនទឹកប្រាក់: $${totalVal}%0A------------------%0A🏧 ភ្ញៀវបានបញ្ជាក់ថាបានបង់លុយតាម ABA រួចរាល់ ✅`;

        fetch(`https://api.telegram.org/bot${token}/sendMessage?chat_id=${chat_id}&text=${message}&parse_mode=Markdown`)
        .then(() => {
            alert("អរគុណសម្រាប់ការគាំទ្រ! ការកុម្ម៉ង់ត្រូវបានផ្ញើជូនម្ចាស់ហាងរួចរាល់។");
            cart = []; updateUI();
            document.querySelectorAll('input, textarea').forEach(i => i.value = "");
        });
    }

    renderProducts();
</script>

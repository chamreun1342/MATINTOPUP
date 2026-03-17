<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Smart Store - Full System</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        :root { --main: #0088cc; --orange: #ff9900; --red: #d9534f; --telegram: #24A1DE; --green: #28a745; }
        body { font-family: 'Khmer OS Battambang', sans-serif; background: #f4f7f6; margin: 0; padding-bottom: 90px; }
        .header { background: linear-gradient(135deg, var(--main), #005f91); color: white; text-align: center; padding: 15px; position: sticky; top: 0; z-index: 1000; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
        .container { padding: 12px; max-width: 600px; margin: auto; }
        
        /* Product Style */
        .product-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 12px; }
        .product-card { background: white; border-radius: 15px; padding: 10px; text-align: center; box-shadow: 0 4px 6px rgba(0,0,0,0.05); border: 1px solid #eee; }
        .product-img { width: 100%; aspect-ratio: 1/1; object-fit: cover; border-radius: 10px; margin-bottom: 8px; background: #eee; }
        .product-card h4 { margin: 5px 0; font-size: 13px; height: 38px; overflow: hidden; color: #333; line-height: 1.4; }
        .product-card .price { color: var(--red); font-weight: bold; font-size: 16px; display: block; margin-bottom: 10px; }
        .btn-add { background: var(--orange); color: white; border: none; padding: 10px; border-radius: 25px; width: 100%; font-weight: bold; font-size: 12px; cursor: pointer; }

        /* Pagination */
        .pagination { display: flex; justify-content: center; align-items: center; gap: 8px; margin: 30px 0; flex-wrap: wrap; }
        .page-btn { padding: 8px 16px; border: 1px solid var(--main); background: white; color: var(--main); border-radius: 8px; cursor: pointer; font-weight: bold; }
        .page-btn.active { background: var(--main); color: white; }

        /* Floating Buttons */
        .floating-controls { position: fixed; bottom: 20px; right: 20px; display: flex; flex-direction: column; gap: 12px; z-index: 2000; }
        .float-btn { width: 60px; height: 60px; border-radius: 50%; display: flex; align-items: center; justify-content: center; color: white; box-shadow: 0 5px 20px rgba(0,0,0,0.3); cursor: pointer; text-decoration: none; }
        .bg-telegram { background: var(--telegram); }
        .bg-cart { background: var(--main); position: relative; }
        .badge { position: absolute; top: 0; right: 0; background: var(--red); color: white; font-size: 11px; width: 22px; height: 22px; border-radius: 50%; display: flex; align-items: center; justify-content: center; border: 2px solid white; font-weight: bold; }

        /* Modals */
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.6); z-index: 3000; align-items: flex-end; justify-content: center; }
        .modal-content { background: white; width: 100%; max-width: 500px; border-radius: 25px 25px 0 0; padding: 20px; box-sizing: border-box; max-height: 90vh; overflow-y: auto; }
        .cart-item { display: flex; justify-content: space-between; align-items: center; padding: 10px 0; border-bottom: 1px solid #eee; }
        
        .order-form input, .order-form textarea { width: 100%; padding: 12px; margin-bottom: 10px; border-radius: 10px; border: 1px solid #ddd; box-sizing: border-box; font-family: inherit; font-size: 14px; outline: none; }
        .btn-confirm { background: var(--green); color: white; border: none; padding: 16px; border-radius: 15px; font-size: 17px; font-weight: bold; width: 100%; cursor: pointer; }

        /* QR Modal */
        #qrModal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.9); z-index: 4000; align-items: center; justify-content: center; text-align: center; }
        .qr-box { background: white; padding: 20px; border-radius: 25px; width: 85%; max-width: 320px; }
        .qr-img { width: 100%; border-radius: 15px; border: 3px solid var(--main); cursor: pointer; margin: 15px 0; }

        /* Success Overlay */
        #successOverlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: white; z-index: 5000; align-items: center; justify-content: center; text-align: center; flex-direction: column; }
    </style>
</head>
<body>

<div class="header"><h3>🏪 ហាងអនឡាញទំនើប</h3></div>

<div class="container">
    <div class="product-grid" id="productGrid"></div>
    <div class="pagination" id="pagination"></div>
</div>

<div class="floating-controls">
    <a href="https://t.me/Luck_17" target="_blank" class="float-btn bg-telegram"><i class="fab fa-telegram-plane" style="font-size: 30px;"></i></a>
    <div id="cartBtn" class="float-btn bg-cart" onclick="openCart()">
        <i class="fas fa-shopping-cart" style="font-size: 24px;"></i>
        <div class="badge" id="cartBadge">0</div>
    </div>
</div>

<div class="modal" id="cartModal">
    <div class="modal-content">
        <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:15px;">
            <h3 style="margin:0;">🛒 បញ្ជីទិញអីវ៉ាន់</h3>
            <span onclick="closeCart()" style="font-size:30px; cursor:pointer; color:#999;">&times;</span>
        </div>
        <div id="cartItems"></div>
        <div style="display:flex; justify-content:space-between; font-weight:bold; margin:20px 0; font-size:18px;">
            <span>សរុប:</span> <span id="totalTxt" style="color:var(--red);">$0.00</span>
        </div>
        <div class="order-form">
            <input type="text" id="custName" placeholder="👤 ឈ្មោះរបស់អ្នក">
            <input type="number" id="custPhone" placeholder="📞 លេខទូរសព្ទ">
            <textarea id="custAddress" placeholder="📍 អាសយដ្ឋានដឹកជញ្ជូន"></textarea>
            <button class="btn-confirm" onclick="submitOrder()">✅ បញ្ជាទិញឥឡូវនេះ</button>
        </div>
    </div>
</div>

<div id="qrModal">
    <div class="qr-box">
        <h3 style="color:var(--main); margin:0;">ចុចលើរូប QR ដើម្បីបង់ប្រាក់</h3>
        <h2 style="color:var(--red); margin:10px 0;">$ <span id="payAmount">0.00</span></h2>
        <a id="bankLink" href="#" target="_blank">
            <img src="aba_mobile.jpg" class="qr-img" title="ចុចបង់ប្រាក់">
        </a>
        <button onclick="sendPaymentNoti()" style="background:var(--green); color:white; border:none; padding:12px; width:100%; border-radius:10px; font-weight:bold; cursor:pointer;">ខ្ញុំបានបង់ប្រាក់រួចរាល់</button>
    </div>
</div>

<div id="successOverlay">
    <i class="fas fa-check-circle" style="font-size: 80px; color: var(--green); margin-bottom: 20px;"></i>
    <h2>ការកុម្ម៉ង់បានជោគជ័យ!</h2>
    <p style="color:gray;">អរគុណច្រើន! យើងនឹងទាក់ទងទៅអ្នកឆាប់ៗ។</p>
    <button onclick="location.reload()" style="background:var(--main); color:white; border:none; padding:12px 30px; border-radius:25px; margin-top:20px; font-weight:bold;">ត្រឡប់ទៅហាងវិញ</button>
</div>

<script>
    const allProducts = [
        { id: 1, name: "ផលិតផលទី១", price: "10.00", img: "https://via.placeholder.com/150" },
        { id: 2, name: "ផលិតផលទី២", price: "15.00", img: "https://via.placeholder.com/150" },
        { id: 1, name: "ផលិតផលទី១", price: "10.00", img: "https://via.placeholder.com/150" },
        { id: 2, name: "ផលិតផលទី២", price: "15.00", img: "https://via.placeholder.com/150" },
        { id: 1, name: "ផលិតផលទី១", price: "10.00", img: "https://via.placeholder.com/150" },
        { id: 2, name: "ផលិតផលទី២", price: "15.00", img: "https://via.placeholder.com/150" },
        { id: 1, name: "ផលិតផលទី១", price: "10.00", img: "https://via.placeholder.com/150" },
        { id: 2, name: "ផលិតផលទី២", price: "15.00", img: "https://via.placeholder.com/150" },
        { id: 1, name: "ផលិតផលទី១", price: "10.00", img: "https://via.placeholder.com/150" },
        { id: 2, name: "ផលិតផលទី២", price: "15.00", img: "https://via.placeholder.com/150" },
        { id: 1, name: "ផលិតផលទី១", price: "10.00", img: "https://via.placeholder.com/150" },
        { id: 2, name: "ផលិតផលទី២", price: "15.00", img: "https://via.placeholder.com/150" },
        // ... ថែមផលិតផលបន្តបន្ទាប់នៅទីនេះ ...
    ];

    let cart = [];
    let curPage = 1;
    const limit = 10;
    const bot_token = "8502623825:AAE9MFP9sQXkqEBdHeQ9oZnp9TxU6g5mL3Y";
    const chat_id = "1643504321";

    function renderProducts(page) {
        curPage = page;
        const grid = document.getElementById('productGrid');
        const start = (page - 1) * limit;
        const items = allProducts.slice(start, start + limit);
        grid.innerHTML = items.map(p => `
            <div class="product-card">
                <img src="${p.img}" class="product-img" onerror="this.src='https://via.placeholder.com/150?text=Product'">
                <h4>${p.name}</h4>
                <span class="price">$${p.price}</span>
                <button class="btn-add" onclick="addToCart(${p.id})">+ ដាក់កន្ត្រក</button>
            </div>
        `).join('');
        renderPager();
        window.scrollTo({top: 0, behavior: 'smooth'});
    }

    function renderPager() {
        const pager = document.getElementById('pagination');
        const totalPages = Math.ceil(allProducts.length / limit);
        pager.innerHTML = "";
        if (totalPages <= 1) return;
        for (let i = 1; i <= totalPages; i++) {
            pager.innerHTML += `<button class="page-btn ${i === curPage ? 'active' : ''}" onclick="renderProducts(${i})">${i}</button>`;
        }
    }

    function addToCart(id) {
        const item = allProducts.find(x => x.id === id);
        cart.push({...item, cartUid: Date.now() + Math.random()});
        document.getElementById('cartBadge').innerText = cart.length;
    }

    function openCart() {
        const list = document.getElementById('cartItems');
        let total = 0;
        list.innerHTML = cart.map(p => {
            total += parseFloat(p.price);
            return `<div class="cart-item"><span>${p.name} ($${p.price})</span><i class="fas fa-trash-alt" style="color:red; cursor:pointer;" onclick="removeItem(${p.cartUid})"></i></div>`;
        }).join('');
        document.getElementById('totalTxt').innerText = `$${total.toFixed(2)}`;
        document.getElementById('cartModal').style.display = 'flex';
    }

    function removeItem(uid) {
        cart = cart.filter(p => p.cartUid !== uid);
        document.getElementById('cartBadge').innerText = cart.length;
        if (cart.length === 0) closeCart(); else openCart();
    }

    function closeCart() { document.getElementById('cartModal').style.display = 'none'; }

    function submitOrder() {
        const name = document.getElementById('custName').value;
        const phone = document.getElementById('custPhone').value;
        const addr = document.getElementById('custAddress').value;
        if (!name || !phone || !addr) return alert("សូមបំពេញព័ត៌មានឱ្យគ្រប់!");

        const total = cart.reduce((s, x) => s + parseFloat(x.price), 0).toFixed(2);
        const productList = cart.map((p, i) => `${i+1}. ${p.name} ($${p.price})`).join('%0A');

        const msg = `📦 *កុម្ម៉ង់ថ្មី*%0A👤 ឈ្មោះ: ${name}%0A📞 លេខ: ${phone}%0A📍 ទីតាំង: ${addr}%0A🛒 *ទំនិញ៖*%0A${productList}%0A💰 *សរុប៖ $${total}*`;
        fetch(`https://api.telegram.org/bot${bot_token}/sendMessage?chat_id=${chat_id}&text=${msg}&parse_mode=Markdown`);

        document.getElementById('payAmount').innerText = total;
        document.getElementById('bankLink').href = `https://link.payway.com.kh/aba?id=oRF8/quqnct3c&amount=${total}`;
        document.getElementById('qrModal').style.display = 'flex';
    }

    // --- កែសម្រួលសារបញ្ជាក់ការបង់ប្រាក់ឱ្យដូចក្នុងរូបថត ---
    function sendPaymentNoti() {
        const total = document.getElementById('payAmount').innerText;
        
        // រៀបចំសារតាមទម្រង់រូបភាពដែលអ្នកផ្ញើមក
        const payMsg = `💰 សរុប: $${total}%0A🏧 ស្ថានភាព: កំពុងរង់ចាំស្កេនបង់ប្រាក់...`;

        fetch(`https://api.telegram.org/bot${bot_token}/sendMessage?chat_id=${chat_id}&text=${payMsg}&parse_mode=Markdown`)
        .then(() => {
            document.getElementById('qrModal').style.display = 'none';
            document.getElementById('cartModal').style.display = 'none';
            document.getElementById('successOverlay').style.display = 'flex';
        });
    }

    renderProducts(1);
</script>

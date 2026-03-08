# new6
<!DOCTYPE html>
<html>
<head>
<title>Smart Product Recommendation & Price Comparison</title>

<style>

body{
font-family:Arial;
margin:0;
background:#f3f4f6;
}

header{
background:#1d4ed8;
color:white;
padding:15px;
text-align:center;
font-size:24px;
}

.container{
padding:20px;
}

.page{
display:none;
}

input{
padding:10px;
margin:5px;
border-radius:5px;
border:1px solid #ccc;
width:220px;
}

button{
padding:10px 15px;
margin:5px;
border:none;
background:#2563eb;
color:white;
border-radius:5px;
cursor:pointer;
}

button:hover{
background:#1e40af;
}

.products{
display:grid;
grid-template-columns:repeat(auto-fill,minmax(250px,1fr));
gap:20px;
margin-top:20px;
}

.card{
background:white;
border-radius:10px;
padding:15px;
box-shadow:0 3px 10px rgba(0,0,0,0.1);
}

.card img{
width:100%;
height:160px;
object-fit:cover;
border-radius:5px;
}

.rating{
color:#f59e0b;
}

.pricebox{
background:#eef2ff;
padding:6px;
margin-top:5px;
border-radius:5px;
}

.bestprice{
background:#16a34a;
color:white;
padding:5px;
border-radius:5px;
display:inline-block;
margin-top:5px;
}

#historyBox{
margin-top:10px;
background:white;
padding:10px;
border-radius:6px;
}

</style>

</head>

<body>

<header>
Smart Product Recommendation & Price Comparison
</header>

<!-- LOGIN PAGE -->

<div id="loginPage" class="container page" style="display:block">

<h2>Select Login</h2>

<button onclick="showUserLogin()">User Login</button>
<button onclick="showAdminLogin()">Admin Login</button>

<div id="userLogin" style="display:none">

<h3>User Login</h3>

<input id="mobile" placeholder="Mobile Number">

<br>

<select id="location">
<option value="">Select Location</option>
<option>Chennai</option>
<option>Madurai</option>
<option>Trichy</option>
<option>Coimbatore</option>
</select>

<br>

<button onclick="login()">Enter Website</button>

</div>

<div id="adminLogin" style="display:none">

<h3>Admin Login</h3>

<input id="adminUser" placeholder="Admin Username">
<input id="adminPass" type="password" placeholder="Password">

<br>

<button onclick="adminLogin()">Login</button>

</div>

</div>

<!-- MAIN PAGE -->

<div id="mainPage" class="container page">

<button onclick="goBack()">⬅ Go Back</button>

<h3>Welcome <span id="userMobile"></span></h3>
<p>Location: <b id="userLocation"></b></p>

<input id="searchInput" placeholder="Search product">
<button onclick="searchProduct()">Search</button>

<div id="historyBox"></div>

<div id="dynamicFilters"></div>

<h3>Recommended Products</h3>
<div id="recommend"></div>

<h3>Products</h3>

<div class="products" id="productList"></div>

</div>

<!-- ADMIN PAGE -->

<div id="adminPage" class="container page">

<h2>Admin Panel</h2>

<input id="pname" placeholder="Product Name">
<input id="ptype" placeholder="Product Type">
<input id="pimage" placeholder="Image URL">

<br>

<button onclick="addProduct()">Add Product</button>
<button onclick="goBack()">Back</button>

</div>

<script>

let searchHistory=[];

let products=[

{
name:"iPhone 14",
type:"mobile",
brand:"Apple",
image:"https://images.unsplash.com/photo-1511707171634",
rating:"4.8",
review:"Best camera phone",

online:{
amazon:78999,
flipkart:78499
},

shops:[
{name:"Reliance Digital",price:79000},
{name:"Poorvika Mobiles",price:78500},
{name:"Sangeetha Mobiles",price:77999}
]

},

{
name:"Samsung Galaxy S23",
type:"mobile",
brand:"Samsung",
image:"https://images.unsplash.com/photo-1580910051074",
rating:"4.7",
review:"Smooth performance",

online:{
amazon:70999,
flipkart:69999
},

shops:[
{name:"Reliance Digital",price:72000},
{name:"Poorvika Mobiles",price:71500},
{name:"Sangeetha Mobiles",price:70999}
]

},

{
name:"Nivea Face Wash",
type:"skin",
skin:"oily",
image:"https://images.unsplash.com/photo-1596755389378",
rating:"4.4",
review:"Best for oily skin",

online:{
amazon:249,
flipkart:239
},

shops:[
{name:"Health Store",price:250},
{name:"Apollo Pharmacy",price:240}
]

},

{
name:"Parachute Hair Oil",
type:"hair",
hair:"normal",
image:"https://images.unsplash.com/photo-1608248597279",
rating:"4.5",
review:"Healthy hair oil",

online:{
amazon:180,
flipkart:175
},

shops:[
{name:"Medical Shop",price:180},
{name:"Super Market",price:170}
]

},

{
name:"Wooden Sofa",
type:"furniture",
brand:"IKEA",
color:"brown",
image:"https://images.unsplash.com/photo-1505693416388",
rating:"4.6",
review:"Comfortable sofa",

online:{
amazon:25500,
flipkart:25000
},

shops:[
{name:"IKEA Store",price:25000},
{name:"Furniture World",price:24000}
]

}

];

function showUserLogin(){
document.getElementById("userLogin").style.display="block";
document.getElementById("adminLogin").style.display="none";
}

function showAdminLogin(){
document.getElementById("adminLogin").style.display="block";
document.getElementById("userLogin").style.display="none";
}

function login(){

let mobile=document.getElementById("mobile").value;
let location=document.getElementById("location").value;

if(mobile.length>=10 && location!=""){

document.getElementById("loginPage").style.display="none";
document.getElementById("mainPage").style.display="block";

document.getElementById("userMobile").innerText=mobile;
document.getElementById("userLocation").innerText=location;

displayProducts(products);
showRecommend();

}
else{
alert("Enter mobile and location");
}

}

function adminLogin(){

let user=document.getElementById("adminUser").value;
let pass=document.getElementById("adminPass").value;

if(user=="admin" && pass=="1234"){

document.getElementById("loginPage").style.display="none";
document.getElementById("adminPage").style.display="block";

}
else{
alert("Invalid login");
}

}

function searchProduct(){

let query=document.getElementById("searchInput").value.toLowerCase();

searchHistory.push(query);

updateHistory();

let result=products.filter(p=>JSON.stringify(p).toLowerCase().includes(query));

displayProducts(result);

showFilters(query);

}

function updateHistory(){

let html="<b>Recent Searches</b><br>";

searchHistory.slice(-5).forEach(h=>{
html+=h+"<br>";
});

document.getElementById("historyBox").innerHTML=html;

}

function showFilters(query){

let html="";

if(query.includes("mobile")){

html=`<h3>Brand</h3>
<button onclick="filterBrand('Apple')">Apple</button>
<button onclick="filterBrand('Samsung')">Samsung</button>`;

}

if(query.includes("skin")){

html=`<h3>Skin Type</h3>
<button onclick="filterSkin('oily')">Oily</button>`;

}

if(query.includes("hair")){

html=`<h3>Hair Type</h3>
<button onclick="filterHair('normal')">Normal</button>`;

}

if(query.includes("furniture")){

html=`<h3>Color</h3>
<button onclick="filterColor('brown')">Brown</button>`;

}

document.getElementById("dynamicFilters").innerHTML=html;

}

function filterBrand(b){
displayProducts(products.filter(p=>p.brand==b));
}

function filterSkin(s){
displayProducts(products.filter(p=>p.skin==s));
}

function filterHair(h){
displayProducts(products.filter(p=>p.hair==h));
}

function filterColor(c){
displayProducts(products.filter(p=>p.color==c));
}

function displayProducts(list){

let html="";

list.forEach(p=>{

let shopHTML="";
let best=Infinity;
let bestShop="";

p.shops.forEach(s=>{
shopHTML+=`<div class="pricebox">🏪 ${s.name} : ₹${s.price}</div>`;
if(s.price<best){
best=s.price;
bestShop=s.name;
}
});

html+=`

<div class="card">

<img src="${p.image}">

<h4>${p.name}</h4>

<p class="rating">⭐ ${p.rating}</p>

<p>${p.review}</p>

<div class="bestprice">
Best Price ₹${best} at ${bestShop}
</div>

<h4>Online Prices</h4>

<div class="pricebox">Amazon ₹${p.online.amazon}</div>
<div class="pricebox">Flipkart ₹${p.online.flipkart}</div>

<h4>Local Shops</h4>

${shopHTML}

</div>

`;

});

document.getElementById("productList").innerHTML=html;

}

function showRecommend(){

let html="";

products.slice(0,3).forEach(p=>{
html+=p.name+" | ";
});

document.getElementById("recommend").innerHTML=html;

}

function goBack(){

document.getElementById("adminPage").style.display="none";
document.getElementById("mainPage").style.display="block";

}

function addProduct(){

let name=document.getElementById("pname").value;
let type=document.getElementById("ptype").value;
let image=document.getElementById("pimage").value;

products.push({
name:name,
type:type,
image:image,
rating:"4",
review:"New Product",
online:{amazon:100,flipkart:95},
shops:[{name:"Local Shop",price:90}]
});

alert("Product Added");

}

</script>

</body>
</html>

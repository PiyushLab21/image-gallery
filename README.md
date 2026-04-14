# image-gallery
<!DOCTYPE html><html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Ultimate Image App</title>
<style>
body{margin:0;font-family:Arial;background:#0f172a;color:#fff}
header{text-align:center;padding:15px;background:#020617}
.container{padding:10px;text-align:center}
input,button{padding:8px;margin:5px;border-radius:6px;border:none}
button{cursor:pointer}
.gallery{display:grid;grid-template-columns:repeat(auto-fit,minmax(200px,1fr));gap:15px;padding:15px}
.card{position:relative}
.card img{width:100%;height:200px;object-fit:cover;border-radius:10px}
.actions{position:absolute;bottom:5px;left:5px;right:5px;display:flex;justify-content:space-between}
.lightbox{display:none;position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.9);justify-content:center;align-items:center}
.lightbox img{max-width:90%;max-height:90%}
</style>
</head>
<body><header>🚀 Ultimate Image App</header><div class="container" id="auth">
  <input id="username" placeholder="Enter username">
  <button onclick="login()">Login</button>
</div><div class="container" id="app" style="display:none">
  <input type="file" id="upload" multiple accept="image/*">
  <input type="text" id="search" placeholder="Search">
</div><div class="gallery" id="gallery"></div><div class="lightbox" id="lightbox">
  <img id="lightbox-img">
</div><script>
let currentUser = localStorage.getItem('user');
let images = JSON.parse(localStorage.getItem('images')||'{}');

function login(){
  const user = document.getElementById('username').value;
  if(!user) return alert('Enter username');
  localStorage.setItem('user', user);
  location.reload();
}

if(currentUser){
  document.getElementById('auth').style.display='none';
  document.getElementById('app').style.display='block';
}

const gallery = document.getElementById('gallery');
const upload = document.getElementById('upload');
const search = document.getElementById('search');
const lightbox = document.getElementById('lightbox');
const lightboxImg = document.getElementById('lightbox-img');

function render(){
  gallery.innerHTML='';
  let userImages = images[currentUser] || [];
  const q = search.value.toLowerCase();

  userImages.filter(i=>i.name.toLowerCase().includes(q))
  .forEach((img,index)=>{
    const card=document.createElement('div');
    card.className='card';

    const image=document.createElement('img');
    image.src=img.src;
    image.onclick=()=>{lightbox.style.display='flex';lightboxImg.src=img.src}

    const actions=document.createElement('div');
    actions.className='actions';

    const like=document.createElement('button');
    like.innerText='❤️ '+(img.likes||0);
    like.onclick=()=>{
      img.likes=(img.likes||0)+1;
      save();
    }

    const del=document.createElement('button');
    del.innerText='❌';
    del.onclick=()=>{
      userImages.splice(index,1);
      save();
    }

    actions.appendChild(like);
    actions.appendChild(del);

    card.appendChild(image);
    card.appendChild(actions);
    gallery.appendChild(card);
  });
}

function save(){
  images[currentUser]=images[currentUser]||[];
  localStorage.setItem('images', JSON.stringify(images));
  render();
}

upload?.addEventListener('change',function(){
  let userImages = images[currentUser]||[];
  [...this.files].forEach(file=>{
    const reader=new FileReader();
    reader.onload=e=>{
      userImages.push({src:e.target.result,name:file.name,likes:0});
      images[currentUser]=userImages;
      save();
    }
    reader.readAsDataURL(file);
  })
});

search?.addEventListener('input',render);
lightbox.onclick=()=>lightbox.style.display='none';

render();
</script></body>
</html>

import React, { useState, useEffect, useMemo, useRef } from 'react';
import { 
  ShoppingBag, 
  Search, 
  Menu, 
  X, 
  Star, 
  Instagram, 
  Facebook, 
  Twitter,
  ArrowRight,
  Trash2,
  Plus,
  Minus,
  Edit,
  PlusCircle,
  Save,
  LogOut,
  Camera,
  AlertCircle,
  Package,
  Home,
  LayoutGrid,
  ChevronLeft,
  ChevronRight
} from 'lucide-react';

// --- DATA AWAL ---
const INITIAL_PRODUCTS = [
  { id: 1, name: "Midnight Oud", category: "Pria", price: 1250000, discountPrice: 1100000, rating: 4.9, image: "https://images.unsplash.com/photo-1541643600914-78b084683601?auto=format&fit=crop&q=80&w=400", description: "Wangi kayu yang dalam dan misterius.", brand: "DONIX Luxury" },
  { id: 2, name: "Rose Silk", category: "Wanita", price: 950000, discountPrice: null, rating: 4.8, image: "https://images.unsplash.com/photo-1592945403244-b3fbafd7f539?auto=format&fit=crop&q=80&w=400", description: "Kelembutan kelopak mawar pagi hari.", brand: "DONIX Essence" },
  { id: 3, name: "Ocean Breeze", category: "Unisex", price: 780000, discountPrice: 650000, rating: 4.7, image: "https://images.unsplash.com/photo-1523293182086-7651a899d37f?auto=format&fit=crop&q=80&w=400", description: "Kesegaran pantai tropis yang tak terbatas.", brand: "DONIX Fresh" },
];

const PROMO_SLIDES = [
  {
    image: "https://images.unsplash.com/photo-1547881023-da79069695be?auto=format&fit=crop&q=80&w=1600",
    title: "The New Gold Collection",
    subtitle: "Aura kemewahan dalam setiap tetesan",
    tag: "Koleksi Terbatas"
  },
  {
    image: "https://images.unsplash.com/photo-1594035910387-fea47794261f?auto=format&fit=crop&q=80&w=1600",
    title: "Summer Breeze Sale",
    subtitle: "Diskon hingga 30% untuk koleksi Ocean",
    tag: "Promo Musim Panas"
  },
  {
    image: "https://images.unsplash.com/photo-1557170334-a7c3c40d002d?auto=format&fit=crop&q=80&w=1600",
    title: "Midnight Oud Premium",
    subtitle: "Varian terbaik untuk pria karismatik",
    tag: "Best Seller"
  }
];

// --- HELPERS ---
const formatRupiah = (number) => new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR', minimumFractionDigits: 0 }).format(number);

// --- COMPONENTS ---

const Navbar = ({ cartCount, onOpenCart, onNavigate, isAdmin, onLogout, activePage }) => {
  const [isScrolled, setIsScrolled] = useState(false);
  const [mobileMenuOpen, setMobileMenuOpen] = useState(false);

  useEffect(() => {
    const handleScroll = () => setIsScrolled(window.scrollY > 20);
    window.addEventListener('scroll', handleScroll);
    return () => window.removeEventListener('scroll', handleScroll);
  }, []);

  const navClass = isScrolled || activePage !== 'home' ? 'bg-white shadow-md py-3 text-black' : 'bg-transparent py-5 text-white';

  const menuItems = [
    { label: 'Home', value: 'home' },
    { label: 'Shop', value: 'shop' },
  ];

  return (
    <nav className={`fixed top-0 left-0 right-0 z-50 transition-all duration-300 ${navClass}`}>
      <div className="max-w-7xl mx-auto px-4 md:px-8 flex justify-between items-center">
        <div className="flex items-center gap-2 cursor-pointer" onClick={() => onNavigate('home')}>
          <div className="w-8 h-8 bg-[#C5A059] rounded-full flex items-center justify-center font-serif font-bold text-white">D</div>
          <h1 className="text-xl font-serif font-bold tracking-widest">DONIX</h1>
        </div>

        <div className="hidden md:flex gap-10 font-medium tracking-widest uppercase text-[10px]">
          {menuItems.map(item => (
            <button 
              key={item.value} 
              onClick={() => onNavigate(item.value)} 
              className={`hover:text-[#C5A059] transition-all relative py-1 ${activePage === item.value ? 'text-[#C5A059] after:content-[""] after:absolute after:bottom-0 after:left-0 after:w-full after:h-0.5 after:bg-[#C5A059]' : ''}`}
            >
              {item.label}
            </button>
          ))}
          {!isAdmin ? (
            <button onClick={() => onNavigate('login')} className="hover:text-[#C5A059] transition-colors border border-[#C5A059] px-4 py-1 rounded-full text-[#C5A059] hover:bg-[#C5A059] hover:text-white">Seller Portal</button>
          ) : (
            <button onClick={() => onNavigate('admin')} className={`text-[#C5A059] font-bold flex items-center gap-1 ${activePage === 'admin' ? 'underline underline-offset-4' : ''}`}>
              <Package size={14}/> Dashboard
            </button>
          )}
        </div>

        <div className="flex items-center gap-3 md:gap-5">
          <button onClick={onOpenCart} className="relative group p-2">
            <ShoppingBag size={22} className="group-hover:text-[#C5A059] transition-colors" />
            {cartCount > 0 && (
              <span className="absolute top-0 right-0 bg-[#C5A059] text-white text-[9px] font-bold w-4 h-4 flex items-center justify-center rounded-full">
                {cartCount}
              </span>
            )}
          </button>
          {isAdmin && (
            <button onClick={onLogout} className="text-red-500 hover:text-red-700 md:block hidden p-2">
              <LogOut size={20} />
            </button>
          )}
          <button className="md:hidden p-2" onClick={() => setMobileMenuOpen(true)}>
            <Menu size={24} />
          </button>
        </div>
      </div>

      {mobileMenuOpen && (
        <div className="fixed inset-0 bg-black z-[60] flex flex-col items-center justify-center gap-10 text-white animate-fadeIn">
          <button className="absolute top-6 right-6" onClick={() => setMobileMenuOpen(false)}>
            <X size={32} />
          </button>
          {menuItems.map(item => (
            <button key={item.value} onClick={() => { onNavigate(item.value); setMobileMenuOpen(false); }} className={`text-2xl font-serif uppercase tracking-widest ${activePage === item.value ? 'text-[#C5A059]' : ''}`}>{item.label}</button>
          ))}
          {isAdmin ? (
            <button onClick={() => { onNavigate('admin'); setMobileMenuOpen(false); }} className="text-2xl font-serif uppercase text-[#C5A059]">Dashboard</button>
          ) : (
            <button onClick={() => { onNavigate('login'); setMobileMenuOpen(false); }} className="text-2xl font-serif uppercase">Login Penjual</button>
          )}
          {isAdmin && <button onClick={onLogout} className="text-red-500 font-bold uppercase tracking-widest mt-4">Logout</button>}
        </div>
      )}
    </nav>
  );
};

export default function App() {
  const [products, setProducts] = useState(INITIAL_PRODUCTS);
  const [currentPage, setCurrentPage] = useState('home');
  const [cart, setCart] = useState([]);
  const [isCartOpen, setIsCartOpen] = useState(false);
  const [filter, setFilter] = useState('All');
  const [searchQuery, setSearchQuery] = useState('');
  
  // Admin & UI States
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const [loginError, setLoginError] = useState('');
  const [editingProduct, setEditingProduct] = useState(null);
  const [showProductModal, setShowProductModal] = useState(false);
  const [currentSlide, setCurrentSlide] = useState(0);
  
  // File Upload Handling
  const fileInputRef = useRef(null);
  const [previewImage, setPreviewImage] = useState("");

  // Slideshow Effect
  useEffect(() => {
    if (currentPage === 'home') {
      const timer = setInterval(() => {
        setCurrentSlide(prev => (prev + 1) % PROMO_SLIDES.length);
      }, 5000);
      return () => clearInterval(timer);
    }
  }, [currentPage]);

  // Cart Handlers
  const addToCart = (product) => {
    setCart(prev => {
      const existing = prev.find(item => item.id === product.id);
      if (existing) {
        return prev.map(item => item.id === product.id ? { ...item, quantity: item.quantity + 1 } : item);
      }
      return [...prev, { ...product, quantity: 1 }];
    });
    setIsCartOpen(true);
  };

  const removeFromCart = (id) => setCart(prev => prev.filter(item => item.id !== id));
  const updateQuantity = (id, delta) => {
    setCart(prev => prev.map(item => item.id === id ? { ...item, quantity: Math.max(1, item.quantity + delta) } : item));
  };

  const cartTotal = useMemo(() => cart.reduce((sum, item) => sum + ((item.discountPrice || item.price) * item.quantity), 0), [cart]);

  // Checkout (WhatsApp & Email)
  const handleCheckout = (type) => {
    const itemsList = cart.map((item, idx) => `${idx + 1}. *${item.name}* (${item.quantity}x) - ${formatRupiah((item.discountPrice || item.price) * item.quantity)}`).join('%0A');
    const message = `*PESANAN BARU - DONIX PARFUME*%0A--------------------------------------------%0A%0A` +
      `Halo Admin,%0A%0ASaya ingin memesan:%0A%0A${itemsList}%0A%0A` +
      `*Total: ${formatRupiah(cartTotal)}*%0A%0A--------------------------------------------%0A` +
      `Mohon segera diproses. Terima kasih!`;

    if (type === 'wa') {
      window.open(`https://wa.me/62881022362914?text=${message}`, '_blank');
    } else {
      window.location.href = `mailto:dalfarizzi93@gmail.com?subject=Order Donix Parfume&body=${message.replace(/%0A/g, '\n').replace(/\*/g, '')}`;
    }
  };

  // Login Handler
  const handleLogin = (e) => {
    e.preventDefault();
    const { username, password } = e.target.elements;
    if (username.value === "Donix" && password.value === "Doni0896") {
      setIsLoggedIn(true);
      setLoginError('');
      setCurrentPage('admin');
    } else {
      setLoginError('Invalid Username or Password');
    }
  };

  // Upload Photo Handler
  const handleFileUpload = (e) => {
    const file = e.target.files[0];
    if (file) {
      const reader = new FileReader();
      reader.onloadend = () => setPreviewImage(reader.result);
      reader.readAsDataURL(file);
    }
  };

  // CRUD Handler
  const handleSaveProduct = (e) => {
    e.preventDefault();
    const formData = new FormData(e.target);
    const productData = {
      id: editingProduct?.id || Date.now(),
      name: formData.get('name'),
      description: formData.get('description'),
      category: formData.get('category'),
      price: parseInt(formData.get('price')),
      discountPrice: formData.get('discountPrice') ? parseInt(formData.get('discountPrice')) : null,
      image: previewImage || formData.get('imageUrl') || "https://images.unsplash.com/photo-1541643600914-78b084683601?auto=format&fit=crop&q=80&w=400"
    };

    if (editingProduct) {
      setProducts(prev => prev.map(p => p.id === editingProduct.id ? productData : p));
    } else {
      setProducts(prev => [productData, ...prev]);
    }
    setShowProductModal(false);
    setEditingProduct(null);
    setPreviewImage("");
  };

  const filteredProducts = products.filter(p => {
    const matchesFilter = filter === 'All' || p.category === filter;
    const matchesSearch = p.name.toLowerCase().includes(searchQuery.toLowerCase());
    return matchesFilter && matchesSearch;
  });

  return (
    <div className="min-h-screen bg-white font-sans text-gray-900 selection:bg-[#C5A059] selection:text-white">
      <Navbar 
        cartCount={cart.reduce((s, i) => s + i.quantity, 0)} 
        onOpenCart={() => setIsCartOpen(true)}
        onNavigate={setCurrentPage}
        isAdmin={isLoggedIn}
        onLogout={() => { setIsLoggedIn(false); setCurrentPage('home'); }}
        activePage={currentPage}
      />

      {/* --- PAGE: HOME (WITH SLIDESHOW) --- */}
      {currentPage === 'home' && (
        <main className="animate-fadeIn">
          <section className="relative h-screen overflow-hidden">
            {PROMO_SLIDES.map((slide, index) => (
              <div 
                key={index}
                className={`absolute inset-0 transition-opacity duration-1000 ease-in-out ${currentSlide === index ? 'opacity-100 z-10' : 'opacity-0 z-0'}`}
              >
                <div className="absolute inset-0 bg-black/50 z-10"></div>
                <img src={slide.image} className="w-full h-full object-cover animate-slowZoom" alt="Slideshow" />
                <div className="absolute inset-0 z-20 flex flex-col items-center justify-center text-center text-white px-4">
                  <span className="text-[#C5A059] text-xs font-bold uppercase tracking-[0.5em] mb-4 bg-white/10 backdrop-blur-md px-4 py-2 rounded-full">{slide.tag}</span>
                  <h2 className="text-5xl md:text-8xl font-serif font-bold mb-6 tracking-tighter">{slide.title}</h2>
                  <p className="text-lg md:text-xl text-gray-300 mb-10 font-light italic tracking-widest">{slide.subtitle}</p>
                  <button onClick={() => setCurrentPage('shop')} className="bg-[#C5A059] text-white px-12 py-5 rounded-full font-bold hover:bg-white hover:text-black transition-all flex items-center justify-center gap-2 group tracking-widest text-[10px] uppercase">
                    Mulai Belanja <ArrowRight size={18} className="group-hover:translate-x-2 transition-transform" />
                  </button>
                </div>
              </div>
            ))}
            {/* Slideshow Controls */}
            <div className="absolute bottom-10 left-1/2 -translate-x-1/2 z-30 flex gap-3">
              {PROMO_SLIDES.map((_, idx) => (
                <button 
                  key={idx} 
                  onClick={() => setCurrentSlide(idx)}
                  className={`w-12 h-1 rounded-full transition-all ${currentSlide === idx ? 'bg-[#C5A059]' : 'bg-white/30 hover:bg-white/50'}`}
                />
              ))}
            </div>
          </section>
        </main>
      )}

      {/* --- PAGE: SHOP --- */}
      {currentPage === 'shop' && (
        <main className="pt-32 pb-24 max-w-7xl mx-auto px-4 md:px-8 animate-fadeIn">
          <div className="flex flex-col md:flex-row gap-12">
            <aside className="w-full md:w-64 space-y-10">
              <div>
                <h3 className="text-[10px] font-bold uppercase tracking-[0.3em] mb-6 border-b pb-4">Filter Kategori</h3>
                <div className="space-y-4">
                  {['All', 'Pria', 'Wanita', 'Unisex'].map(cat => (
                    <button 
                      key={cat} 
                      onClick={() => setFilter(cat)}
                      className={`w-full flex items-center justify-between py-2 text-xs uppercase tracking-widest ${filter === cat ? 'text-[#C5A059] font-bold' : 'text-gray-500 hover:text-black'}`}
                    >
                      {cat}
                      {filter === cat && <div className="w-1.5 h-1.5 bg-[#C5A059] rounded-full"></div>}
                    </button>
                  ))}
                </div>
              </div>
              <div>
                <h3 className="text-[10px] font-bold uppercase tracking-[0.3em] mb-6 border-b pb-4">Pencarian</h3>
                <div className="relative">
                  <input type="text" placeholder="Nama aroma..." className="w-full border-gray-100 bg-gray-50 p-3 rounded-xl focus:ring-1 focus:ring-[#C5A059] outline-none text-xs" onChange={(e) => setSearchQuery(e.target.value)} />
                  <Search size={16} className="absolute right-3 top-3.5 text-gray-400" />
                </div>
              </div>
            </aside>

            <div className="flex-grow">
              <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-10">
                {filteredProducts.map(product => (
                  <div key={product.id} className="group cursor-pointer">
                    <div className="relative overflow-hidden aspect-[3/4] bg-gray-50 mb-6 rounded-2xl shadow-sm">
                      <img src={product.image} className="w-full h-full object-cover group-hover:scale-110 transition-transform duration-700" alt={product.name} />
                      {product.discountPrice && (
                        <div className="absolute top-4 left-4 bg-red-600 text-white text-[9px] px-2 py-1 font-bold uppercase tracking-widest shadow-lg">Sale</div>
                      )}
                      <div className="absolute inset-x-4 bottom-4 translate-y-12 opacity-0 group-hover:translate-y-0 group-hover:opacity-100 transition-all duration-300">
                        <button onClick={() => addToCart(product)} className="w-full bg-black/90 backdrop-blur text-white py-4 rounded-xl font-bold text-[10px] uppercase tracking-widest">Tambah Ke Tas</button>
                      </div>
                    </div>
                    <div className="text-center">
                      <p className="text-[9px] uppercase tracking-widest text-gray-400 mb-2">{product.category}</p>
                      <h3 className="font-serif font-bold text-xl mb-1 group-hover:text-[#C5A059] transition-colors">{product.name}</h3>
                      <div className="flex items-center justify-center gap-3">
                        <p className="text-[#C5A059] font-bold">{formatRupiah(product.discountPrice || product.price)}</p>
                        {product.discountPrice && <p className="text-gray-300 text-sm line-through decoration-gray-300">{formatRupiah(product.price)}</p>}
                      </div>
                    </div>
                  </div>
                ))}
              </div>
            </div>
          </div>
        </main>
      )}

      {/* --- PAGE: LOGIN --- */}
      {currentPage === 'login' && (
        <main className="pt-40 pb-20 flex justify-center px-4 bg-gray-50 min-h-screen">
          <div className="max-w-md w-full bg-white border border-gray-100 p-10 shadow-2xl rounded-2xl animate-fadeIn h-fit">
            <div className="text-center mb-8">
              <div className="w-16 h-16 bg-[#C5A059] rounded-full flex items-center justify-center font-serif font-bold text-white text-3xl mx-auto mb-4">D</div>
              <h2 className="text-3xl font-serif font-bold">Seller Portal</h2>
              <p className="text-gray-400 text-sm mt-2">Masuk untuk mengelola produk Anda</p>
            </div>
            <form onSubmit={handleLogin} className="space-y-6">
              {loginError && <div className="bg-red-50 text-red-600 p-3 rounded-lg text-xs flex items-center gap-2"><AlertCircle size={14}/> {loginError}</div>}
              <div>
                <label className="block text-[10px] font-bold uppercase tracking-widest mb-2 text-gray-400">Username</label>
                <input name="username" type="text" className="w-full border-gray-200 p-3 rounded-xl focus:ring-1 focus:ring-[#C5A059] outline-none" required />
              </div>
              <div>
                <label className="block text-[10px] font-bold uppercase tracking-widest mb-2 text-gray-400">Password</label>
                <input name="password" type="password" className="w-full border-gray-200 p-3 rounded-xl focus:ring-1 focus:ring-[#C5A059] outline-none" required />
              </div>
              <button type="submit" className="w-full bg-black text-white py-4 rounded-xl font-bold tracking-widest hover:bg-[#C5A059] transition-all uppercase text-xs">Login Admin</button>
            </form>
          </div>
        </main>
      )}

      {/* --- PAGE: ADMIN --- */}
      {currentPage === 'admin' && isLoggedIn && (
        <main className="pt-32 pb-20 max-w-7xl mx-auto px-4 md:px-8 animate-fadeIn">
          <div className="flex flex-col md:flex-row justify-between items-center mb-10 gap-6">
            <div>
              <h1 className="text-4xl font-serif font-bold">Katalog Produk</h1>
              <p className="text-gray-500">Total {products.length} produk aktif dalam database</p>
            </div>
            <button 
              onClick={() => { setEditingProduct(null); setPreviewImage(""); setShowProductModal(true); }}
              className="bg-black text-white px-8 py-3 rounded-full flex items-center gap-2 hover:bg-[#C5A059] transition-all font-bold shadow-lg uppercase text-[10px] tracking-widest"
            >
              <PlusCircle size={18} /> Tambah Produk Baru
            </button>
          </div>

          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
            {products.map(p => (
              <div key={p.id} className="border border-gray-100 rounded-3xl overflow-hidden bg-white shadow-sm hover:shadow-xl transition-all group">
                <div className="relative h-64 overflow-hidden">
                  <img src={p.image} className="w-full h-full object-cover group-hover:scale-105 transition-transform" alt={p.name} />
                  <div className="absolute inset-0 bg-black/40 opacity-0 group-hover:opacity-100 transition-opacity flex items-center justify-center gap-4">
                    <button onClick={() => { setEditingProduct(p); setPreviewImage(p.image); setShowProductModal(true); }} className="p-3 bg-white text-blue-600 rounded-full shadow hover:scale-110 transition-transform"><Edit size={20}/></button>
                    <button onClick={() => setProducts(products.filter(item => item.id !== p.id))} className="p-3 bg-white text-red-600 rounded-full shadow hover:scale-110 transition-transform"><Trash2 size={20}/></button>
                  </div>
                </div>
                <div className="p-6">
                  <div className="flex justify-between items-start mb-2">
                    <span className="text-[9px] uppercase tracking-widest text-[#C5A059] font-bold">{p.category}</span>
                    <span className="text-[10px] text-gray-300 font-bold">#{p.id.toString().slice(-4)}</span>
                  </div>
                  <h3 className="text-xl font-serif font-bold mb-2">{p.name}</h3>
                  <div className="flex items-center justify-between">
                    <div>
                      {p.discountPrice ? (
                        <div className="flex flex-col">
                          <span className="text-red-600 font-bold">{formatRupiah(p.discountPrice)}</span>
                          <span className="text-xs line-through text-gray-400">{formatRupiah(p.price)}</span>
                        </div>
                      ) : <span className="font-bold text-lg">{formatRupiah(p.price)}</span>}
                    </div>
                  </div>
                </div>
              </div>
            ))}
          </div>
        </main>
      )}

      {/* --- PRODUCT MODAL (EDIT/ADD) --- */}
      {showProductModal && (
        <div className="fixed inset-0 z-[110] flex items-center justify-center p-4">
          <div className="absolute inset-0 bg-black/80 backdrop-blur-sm" onClick={() => setShowProductModal(false)}></div>
          <div className="relative bg-white w-full max-w-2xl rounded-3xl shadow-2xl overflow-hidden animate-fadeIn max-h-[90vh] overflow-y-auto">
            <div className="p-6 border-b flex justify-between items-center bg-gray-50">
              <h3 className="text-xl font-serif font-bold">{editingProduct ? 'Edit Informasi Produk' : 'Tambah Produk Baru'}</h3>
              <button onClick={() => setShowProductModal(false)} className="p-2 hover:bg-gray-200 rounded-full transition-colors"><X size={24}/></button>
            </div>
            <form onSubmit={handleSaveProduct} className="p-8 space-y-6">
              {/* Image Preview & Upload */}
              <div className="flex flex-col md:flex-row gap-8 items-center border-b pb-8 border-gray-100">
                <div className="w-32 h-44 bg-gray-50 rounded-2xl overflow-hidden border border-dashed border-gray-200 relative group shrink-0">
                  {previewImage ? (
                    <img src={previewImage} className="w-full h-full object-cover" alt="Preview" />
                  ) : (
                    <div className="flex flex-col items-center justify-center h-full text-gray-300">
                      <Camera size={32} strokeWidth={1}/>
                      <span className="text-[8px] font-bold uppercase mt-2">No Photo</span>
                    </div>
                  )}
                  <button 
                    type="button" 
                    onClick={() => fileInputRef.current.click()}
                    className="absolute inset-0 bg-black/40 opacity-0 group-hover:opacity-100 transition-opacity flex items-center justify-center text-white text-[9px] font-bold uppercase tracking-widest"
                  >
                    Ganti Foto
                  </button>
                  <input ref={fileInputRef} type="file" accept="image/*" className="hidden" onChange={handleFileUpload} />
                </div>
                <div className="flex-grow w-full space-y-4">
                  <div>
                    <label className="block text-[10px] font-bold mb-1 uppercase text-gray-400 tracking-widest">Atau Gunakan URL Gambar</label>
                    <input name="imageUrl" defaultValue={editingProduct?.image} className="w-full border-gray-100 bg-gray-50 p-3 rounded-xl text-xs" placeholder="https://..." onChange={(e) => setPreviewImage(e.target.value)} />
                  </div>
                  <p className="text-[9px] text-gray-400 italic">Gunakan foto dengan rasio 3:4 untuk hasil terbaik.</p>
                </div>
              </div>

              <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div className="md:col-span-2">
                  <label className="block text-[10px] font-bold mb-1 uppercase text-gray-500 tracking-widest">Nama / Judul Produk</label>
                  <input name="name" defaultValue={editingProduct?.name} className="w-full border-gray-200 p-4 rounded-2xl focus:ring-1 focus:ring-[#C5A059] outline-none font-serif font-bold" required />
                </div>
                <div className="md:col-span-2">
                  <label className="block text-[10px] font-bold mb-1 uppercase text-gray-500 tracking-widest">Deskripsi Aroma</label>
                  <textarea name="description" defaultValue={editingProduct?.description} className="w-full border-gray-200 p-4 rounded-2xl focus:ring-1 focus:ring-[#C5A059] outline-none h-24 resize-none" required />
                </div>
                <div>
                  <label className="block text-[10px] font-bold mb-1 uppercase text-gray-500 tracking-widest">Kategori</label>
                  <select name="category" defaultValue={editingProduct?.category || "Pria"} className="w-full border-gray-200 p-4 rounded-2xl">
                    <option>Pria</option>
                    <option>Wanita</option>
                    <option>Unisex</option>
                  </select>
                </div>
                <div>
                  <label className="block text-[10px] font-bold mb-1 uppercase text-gray-500 tracking-widest">Harga Retail (Rp)</label>
                  <input name="price" type="number" defaultValue={editingProduct?.price} className="w-full border-gray-200 p-4 rounded-2xl focus:ring-1 focus:ring-[#C5A059] outline-none" required />
                </div>
                <div className="md:col-span-2">
                  <label className="block text-[10px] font-bold mb-1 uppercase text-[#C5A059] tracking-widest">Harga Diskon (Rp) - Opsional</label>
                  <input name="discountPrice" type="number" defaultValue={editingProduct?.discountPrice} className="w-full border-gray-200 p-4 rounded-2xl focus:ring-1 focus:ring-[#C5A059] outline-none bg-amber-50/20" />
                </div>
              </div>
              <button type="submit" className="w-full bg-black text-white py-5 rounded-2xl font-bold tracking-[0.2em] hover:bg-[#C5A059] transition-all flex items-center justify-center gap-3 uppercase text-xs">
                <Save size={18} /> Simpan Katalog Produk
              </button>
            </form>
          </div>
        </div>
      )}

      {/* --- CART OVERLAY --- */}
      {isCartOpen && (
        <div className="fixed inset-0 z-[120] flex justify-end">
          <div className="absolute inset-0 bg-black/70 backdrop-blur-sm" onClick={() => setIsCartOpen(false)}></div>
          <div className="relative w-full max-w-md bg-white h-full shadow-2xl flex flex-col animate-slideLeft">
            <div className="p-8 border-b flex justify-between items-center bg-gray-50">
              <h2 className="text-xl font-serif font-bold tracking-widest uppercase">Shopping Bag</h2>
              <button onClick={() => setIsCartOpen(false)} className="hover:rotate-90 transition-transform"><X size={28} /></button>
            </div>
            <div className="flex-grow overflow-y-auto p-8 space-y-8">
              {cart.length === 0 ? (
                <div className="h-full flex flex-col items-center justify-center text-gray-300 gap-4">
                  <ShoppingBag size={80} strokeWidth={1} />
                  <p className="italic text-sm">Keranjang masih kosong.</p>
                </div>
              ) : cart.map(item => (
                <div key={item.id} className="flex gap-6 items-center group">
                  <img src={item.image} className="w-20 h-28 object-cover rounded-xl shadow group-hover:shadow-lg transition-all" alt={item.name} />
                  <div className="flex-grow">
                    <h4 className="font-bold text-sm mb-1">{item.name}</h4>
                    <p className="text-[#C5A059] font-medium text-xs mb-4">{formatRupiah(item.discountPrice || item.price)}</p>
                    <div className="flex items-center gap-4 border border-gray-100 w-max px-3 py-1 rounded-full bg-gray-50">
                      <button onClick={() => updateQuantity(item.id, -1)} className="hover:text-[#C5A059]"><Minus size={12}/></button>
                      <span className="text-xs font-bold w-4 text-center">{item.quantity}</span>
                      <button onClick={() => updateQuantity(item.id, 1)} className="hover:text-[#C5A059]"><Plus size={12}/></button>
                    </div>
                  </div>
                  <button onClick={() => removeFromCart(item.id)} className="text-gray-300 hover:text-red-500 transition-colors"><Trash2 size={18} /></button>
                </div>
              ))}
            </div>
            {cart.length > 0 && (
              <div className="p-8 border-t bg-gray-50 space-y-4">
                <div className="flex justify-between items-center mb-2">
                  <span className="text-gray-400 uppercase text-[10px] tracking-widest font-bold">Total Belanja</span>
                  <span className="text-2xl font-serif font-bold">{formatRupiah(cartTotal)}</span>
                </div>
                <div className="grid grid-cols-2 gap-3">
                  <button onClick={() => handleCheckout('wa')} className="col-span-2 bg-[#25D366] text-white py-5 rounded-2xl font-bold tracking-widest hover:bg-[#128C7E] transition-all transform active:scale-95 shadow-lg shadow-green-500/20 flex items-center justify-center gap-2 uppercase text-[10px]">
                    WhatsApp Checkout
                  </button>
                  <button onClick={() => handleCheckout('email')} className="col-span-2 border border-black py-4 rounded-2xl font-bold tracking-widest hover:bg-black hover:text-white transition-all uppercase text-[10px]">
                    Email Order
                  </button>
                </div>
              </div>
            )}
          </div>
        </div>
      )}

      <Footer />

      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,400;0,700;1,400&family=Inter:wght@300;400;500;700&display=swap');
        body { font-family: 'Inter', sans-serif; scroll-behavior: smooth; overflow-x: hidden; }
        .font-serif { font-family: 'Playfair Display', serif; }
        
        @keyframes slideLeft { 
          from { transform: translateX(100%); opacity: 0; } 
          to { transform: translateX(0); opacity: 1; } 
        }
        .animate-slideLeft { animation: slideLeft 0.5s cubic-bezier(0.16, 1, 0.3, 1); }
        
        @keyframes fadeIn { 
          from { opacity: 0; transform: translateY(10px); } 
          to { opacity: 1; transform: translateY(0); } 
        }
        .animate-fadeIn { animation: fadeIn 0.8s ease-out; }

        @keyframes slowZoom {
          from { transform: scale(1); }
          to { transform: scale(1.15); }
        }
        .animate-slowZoom { animation: slowZoom 15s linear infinite alternate; }

        ::-webkit-scrollbar { width: 5px; }
        ::-webkit-scrollbar-track { background: #f1f1f1; }
        ::-webkit-scrollbar-thumb { background: #C5A059; border-radius: 10px; }
      `}</style>
    </div>
  );
}

const Footer = () => (
  <footer className="bg-[#0a0a0a] text-white pt-24 pb-12 border-t border-white/5">
    <div className="max-w-7xl mx-auto px-4 md:px-8 grid grid-cols-1 md:grid-cols-4 gap-16 border-b border-white/5 pb-20">
      <div className="col-span-1 md:col-span-1">
        <h2 className="text-3xl font-serif font-bold tracking-[0.3em] mb-8">DONIX</h2>
        <p className="text-gray-500 text-sm leading-loose mb-8 font-light italic">"Memberikan kemewahan aroma abadi untuk setiap karakter unik Anda."</p>
        <div className="flex gap-4">
          {[Instagram, Facebook, Twitter].map((Icon, idx) => (
            <div key={idx} className="w-10 h-10 rounded-full border border-white/10 flex items-center justify-center hover:bg-[#C5A059] hover:border-[#C5A059] transition-all cursor-pointer group">
              <Icon size={16} className="text-white group-hover:scale-110 transition-transform" />
            </div>
          ))}
        </div>
      </div>
      <div>
        <h3 className="text-[10px] font-bold mb-10 uppercase tracking-[0.4em] text-gray-400">Koleksi</h3>
        <ul className="space-y-6 text-gray-500 text-[10px] tracking-[0.2em] font-medium uppercase">
          <li className="hover:text-[#C5A059] cursor-pointer transition-colors">Parfum Pria</li>
          <li className="hover:text-[#C5A059] cursor-pointer transition-colors">Parfum Wanita</li>
          <li className="hover:text-[#C5A059] cursor-pointer transition-colors">Unisex Collection</li>
          <li className="hover:text-[#C5A059] cursor-pointer transition-colors">New Arrivals</li>
        </ul>
      </div>
      <div>
        <h3 className="text-[10px] font-bold mb-10 uppercase tracking-[0.4em] text-gray-400">Toko</h3>
        <ul className="space-y-6 text-gray-500 text-[10px] tracking-[0.2em] font-medium uppercase">
          <li className="hover:text-[#C5A059] cursor-pointer transition-colors">Kontak Admin</li>
          <li className="hover:text-[#C5A059] cursor-pointer transition-colors">Pengiriman</li>
          <li className="hover:text-[#C5A059] cursor-pointer transition-colors">Tentang Kami</li>
        </ul>
      </div>
      <div>
        <h3 className="text-[10px] font-bold mb-10 uppercase tracking-[0.4em] text-gray-400">Informasi Kontak</h3>
        <p className="text-gray-500 text-[10px] tracking-widest leading-relaxed uppercase mb-3">WhatsApp: +62 881-0223-62914</p>
        <p className="text-gray-500 text-[10px] tracking-widest uppercase">Email: dalfarizzi93@gmail.com</p>
        <div className="mt-10 p-5 border border-[#C5A059]/30 rounded-2xl bg-[#C5A059]/5">
          <p className="text-[9px] text-[#C5A059] italic font-medium leading-relaxed uppercase tracking-wider">Tersedia pengiriman premium ke seluruh wilayah Indonesia.</p>
        </div>
      </div>
    </div>
    <div className="text-center mt-12 text-gray-600 text-[9px] uppercase tracking-[0.5em] font-medium">
      © {new Date().getFullYear()} DONIX PARFUME — Luxury Fragrance Standards.
    </div>
  </footer>
);

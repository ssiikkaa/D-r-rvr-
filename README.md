<!DOCTYPE html>
<html lang="fa" dir="rtl">
  <head>
    <meta charset="UTF-8" />
    <title>تبدیل واحد</title>
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <link href="https://fonts.googleapis.com/css2?family=Vazirmatn:wght@400;600;700&display=swap" rel="stylesheet">
    <script src="https://cdn.tailwindcss.com"></script>
    <script crossorigin src="https://unpkg.com/react@18/umd/react.development.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <style>
      body {
        font-family: "Vazirmatn", sans-serif;
        background: linear-gradient(135deg, #e0f7fa, #fff9c4);
        min-height: 100vh;
      }
      select, input {
        border-radius: 10px;
        border: 1px solid #ccc;
        padding: 8px 12px;
        width: 100%;
        transition: all 0.3s ease;
      }
      select:focus, input:focus {
        border-color: #3b82f6;
        box-shadow: 0 0 0 2px rgba(59,130,246,0.3);
      }
      button {
        transition: all 0.3s ease;
      }
      button:hover {
        transform: scale(1.05);
      }
    </style>
  </head>
  <body class="p-4">
    <div id="root"></div>

    <script type="text/babel">
      const { useState, useMemo } = React;

      function App() {
        const [category, setCategory] = useState("mass");
        const [from, setFrom] = useState("");
        const [to, setTo] = useState("");
        const [amount, setAmount] = useState(1);
        const [result, setResult] = useState(null);
        const [premium, setPremium] = useState(false);
        const [user, setUser] = useState(null);
        const [authVisible, setAuthVisible] = useState(false);

        const categories = useMemo(() => ({
          mass: { label: "وزن", units: { g: "گرم", kg: "کیلوگرم", lb: "پوند", oz: "اونس" } },
          length: { label: "طول", units: { mm: "میلی‌متر", cm: "سانتی‌متر", m: "متر", km: "کیلومتر" } },
          temp: { label: "دما", units: { c: "سانتی‌گراد", f: "فارنهایت", k: "کلوین" } },
          speed: { label: "سرعت", units: { kmh: "کیلومتر/ساعت", ms: "متر/ثانیه", mph: "مایل/ساعت" } },
          volume: { label: "حجم", units: { ml: "میلی‌لیتر", l: "لیتر", m3: "متر مکعب", gal: "گالن" } },
          area: { label: "سطح", units: { m2: "متر مربع", km2: "کیلومتر مربع", cm2: "سانتی‌متر مربع", ha: "هکتار" } },
          energy: { label: "انرژی", units: { j: "ژول", kj: "کیلوژول", cal: "کالری", kcal: "کیلوکالری" } },
          currency: { label: "ارز (ویژه)", units: { usd: "دلار", eur: "یورو", irt: "تومان" }, premium: true }
        }), []);

        const convert = () => {
          if (!from || !to) return;
          const val = parseFloat(amount);
          if (category === "mass") {
            const rates = { g: 1, kg: 1000, lb: 453.592, oz: 28.35 };
            setResult((val * rates[from]) / rates[to]);
          } else if (category === "length") {
            const rates = { mm: 0.001, cm: 0.01, m: 1, km: 1000 };
            setResult((val * rates[from]) / rates[to]);
          } else if (category === "temp") {
            let base;
            if (from === "c") base = val;
            if (from === "f") base = (val - 32) * (5 / 9);
            if (from === "k") base = val - 273.15;
            let out = base;
            if (to === "f") out = base * 9 / 5 + 32;
            if (to === "k") out = base + 273.15;
            setResult(out);
          } else if (category === "speed") {
            const rates = { kmh: 1, ms: 3.6, mph: 1.609 };
            setResult((val * rates[from]) / rates[to]);
          } else if (category === "volume") {
            const rates = { ml: 0.001, l: 1, m3: 1000, gal: 3.78541 };
            setResult((val * rates[from]) / rates[to]);
          } else if (category === "area") {
            const rates = { m2: 1, km2: 1000000, cm2: 0.0001, ha: 10000 };
            setResult((val * rates[from]) / rates[to]);
          } else if (category === "energy") {
            const rates = { j: 1, kj: 1000, cal: 4.184, kcal: 4184 };
            setResult((val * rates[from]) / rates[to]);
          } else if (category === "currency") {
            if (!premium) return alert("برای تبدیل ارز باید ثبت‌نام و اشتراک فعال کنید.");
            const rates = { usd: 1, eur: 0.92, irt: 58000 };
            setResult((val * rates[from]) / rates[to]);
          }
        };

        const handleLogin = () => {
          setUser({ name: "کاربر نمونه" });
          setAuthVisible(false);
        };

        const handlePay = () => {
          alert("پرداخت با موفقیت انجام شد ✅");
          setPremium(true);
        };

        return (
          <div className="max-w-lg mx-auto bg-white/90 shadow-2xl rounded-3xl p-6 mt-6 backdrop-blur-md border border-blue-200">
            <h1 className="text-3xl font-bold text-center mb-6 bg-gradient-to-r from-blue-500 via-green-500 to-yellow-500 bg-clip-text text-transparent">
              🌐 تبدیل واحد
            </h1>

            {/* انتخاب دسته */}
            <div className="mb-3">
              <label className="block mb-1 text-gray-700 font-semibold">نوع تبدیل:</label>
              <select value={category} onChange={e => setCategory(e.target.value)}>
                {Object.entries(categories).map(([key, val]) => (
                  <option key={key} value={key}>{val.label}</option>
                ))}
              </select>
            </div>

            {/* انتخاب واحد‌ها */}
            <div className="grid grid-cols-2 gap-3 mb-3">
              <div>
                <label className="block mb-1 font-semibold">از:</label>
                <select value={from} onChange={e => setFrom(e.target.value)}>
                  <option value="">انتخاب</option>
                  {Object.entries(categories[category].units).map(([k,v]) => (
                    <option key={k} value={k}>{v}</option>
                  ))}
                </select>
              </div>
              <div>
                <label className="block mb-1 font-semibold">به:</label>
                <select value={to} onChange={e => setTo(e.target.value)}>
                  <option value="">انتخاب</option>
                  {Object.entries(categories[category].units).map(([k,v]) => (
                    <option key={k} value={k}>{v}</option>
                  ))}
                </select>
              </div>
            </div>

            {/* مقدار */}
            <div className="mb-3">
              <label className="block mb-1 font-semibold">مقدار:</label>
              <input type="number" value={amount} onChange={e => setAmount(e.target.value)} />
            </div>

            {/* دکمه تبدیل */}
            <button onClick={convert} className="w-full py-2 rounded-xl bg-gradient-to-r from-blue-500 via-green-500 to-yellow-400 text-white font-bold text-lg shadow-md">
              تبدیل کن
            </button>

            {result !== null && (
              <div className="mt-4 bg-blue-50 border border-blue-300 rounded-xl p-3 text-center text-lg text-gray-700 font-semibold shadow-inner">
                نتیجه: {result.toFixed(4)}
              </div>
            )}

            <div className="mt-6 flex justify-between items-center text-sm text-gray-600">
              {user ? (
                <>
                  <span>👤 {user.name}</span>
                  <button onClick={() => setUser(null)} className="text-red-500 hover:underline">خروج</button>
                </>
              ) : (
                <button onClick={() => setAuthVisible(true)} className="text-blue-600 hover:underline">ورود / ثبت‌نام</button>
              )}
            </div>

            {category === "currency" && !premium && (
              <div className="mt-4 p-3 bg-yellow-100 border border-yellow-300 rounded-xl text-center">
                🔒 این بخش ویژه است — برای فعال‌سازی، اشتراک تهیه کنید.
                <button onClick={handlePay} className="block mt-2 mx-auto px-4 py-1 bg-green-500 text-white rounded-lg hover:bg-green-600">خرید اشتراک</button>
              </div>
            )}

            {authVisible && (
              <div className="fixed inset-0 bg-black/30 flex items-center justify-center">
                <div className="bg-white p-6 rounded-2xl w-80 shadow-xl text-center">
                  <h2 className="text-xl font-bold mb-3 text-blue-600">ورود / ثبت‌نام</h2>
                  <input placeholder="نام کاربری" className="mb-3 w-full border rounded-lg px-3 py-2" />
                  <input type="password" placeholder="رمز عبور" className="mb-3 w-full border rounded-lg px-3 py-2" />
                  <button onClick={handleLogin} className="w-full py-2 rounded-lg bg-blue-500 text-white font-semibold hover:bg-blue-600">تأیید</button>
                  <button onClick={() => setAuthVisible(false)} className="mt-2 text-gray-500 hover:underline">بستن</button>
                </div>
              </div>
            )}

            <footer className="mt-8 text-center text-gray-500 text-sm">
              💡 طراحی و برنامه‌نویسی توسط <span className="text-blue-600 font-semibold">مهران کریمی</span>
            </footer>
          </div>
        );
      }

      ReactDOM.createRoot(document.getElementById("root")).render(<App />);
    </script>
  </body>
</html>      <div class="flex items-center space-x-reverse space-x-4">
        <div class="w-12 h-12 bg-yellow-400 rounded-xl flex items-center justify-center animate-float">
          <i class="ti ti-school text-2xl text-blue-900"></i>
        </div>
        <h1 class="text-2xl md:text-3xl font-bold tracking-wide">سامانه دانشگاهی</h1>
      </div>

      <!-- Desktop Nav -->
      <nav class="hidden md:flex items-center space-x-reverse space-x-8">
        <a href="#home" class="hover:text-yellow-300 transition-colors font-medium">خانه</a>
        <a href="#unit" class="hover:text-yellow-300 transition-colors font-medium">انتخاب واحد</a>
        <a href="#dorm" class="hover:text-yellow-300 transition-colors font-medium">خوابگاه</a>
        <a href="#food" class="hover:text-yellow-300 transition-colors font-medium">رزرو غذا</a>
        <a href="#" class="text-yellow-300 font-bold" onclick="logout()">خروج</a>
      </nav>

      <!-- Mobile Menu Button -->
      <button id="mobile-menu-btn" class="md:hidden p-2">
        <i class="ti ti-menu-2 text-2xl"></i>
      </button>
    </div>
  </header>

  <!-- Mobile Menu -->
  <div id="mobile-menu" class="fixed inset-0 bg-slate-900 bg-opacity-95 z-40 hidden flex items-center justify-center">
    <button id="close-menu" class="absolute top-6 left-6 text-white text-3xl">&times;</button>
    <nav class="text-center space-y-8 text-2xl">
      <a href="#home" class="block text-white hover:text-yellow-400 transition">خانه</a>
      <a href="#unit" class="block text-white hover:text-yellow-400 transition">انتخاب واحد</a>
      <a href="#dorm" class="block text-white hover:text-yellow-400 transition">خوابگاه</a>
      <a href="#food" class="block text-white hover:text-yellow-400 transition">رزرو غذا</a>
      <a href="#" class="block text-yellow-400 font-bold" onclick="logout()">خروج</a>
    </nav>
  </div>

  <!-- Hero Section (Home) -->
  <section id="home" class="relative py-24 overflow-hidden">
    <div class="absolute inset-0 bg-gradient-to-br from-blue-500/10 to-indigo-600/10"></div>
    <div class="container mx-auto px-6 text-center relative z-10">
      <h2 data-aos="fade-up" class="text-4xl md:text-6xl font-extrabold text-blue-900 dark:text-blue-300 mb-6">
        به <span class="text-transparent bg-clip-text bg-gradient-to-r from-blue-600 to-indigo-700">سامانه دانشگاه</span> خوش آمدید
      </h2>
      <p data-aos="fade-up" data-aos-delay="200" class="text-lg md:text-xl text-slate-600 dark:text-slate-300 max-w-3xl mx-auto leading-relaxed">
        مدیریت هوشمند انتخاب واحد، رزرو خوابگاه و سفارش غذا — همه در یک مکان، با تجربه‌ای بی‌نظیر.
      </p>
      <div data-aos="fade-up" data-aos-delay="400" class="mt-10 flex justify-center space-x-reverse space-x-4">
        <div class="w-3 h-3 bg-blue-600 rounded-full animate-pulse"></div>
        <div class="w-3 h-3 bg-yellow-400 rounded-full animate-pulse animation-delay-200"></div>
        <div class="w-3 h-3 bg-indigo-600 rounded-full animate-pulse animation-delay-400"></div>
      </div>
    </div>
  </section>

  <!-- Cards Section -->
  <section class="container mx-auto px-6 py-16">
    <div class="grid grid-cols-1 md:grid-cols-3 gap-10 max-w-6xl mx-auto">
      <!-- Card 1 -->
      <div data-aos="zoom-in" data-aos-delay="100" class="card-hover group relative bg-white dark:bg-slate-800 rounded-3xl p-8 shadow-lg overflow-hidden">
        <div class="absolute inset-0 bg-gradient-to-br from-blue-500/5 to-indigo-600/5 opacity-0 group-hover:opacity-100 transition-opacity"></div>
        <div class="relative z-10">
          <div class="w-20 h-20 mx-auto mb-6 bg-gradient-to-br from-blue-500 to-indigo-600 rounded-2xl flex items-center justify-center shadow-xl group-hover:scale-110 transition-transform">
            <i class="ti ti-book text-3xl text-white"></i>
          </div>
          <h3 class="text-2xl font-bold text-blue-900 dark:text-blue-300 mb-3">انتخاب واحد</h3>
          <p class="text-slate-600 dark:text-slate-400 mb-6 leading-relaxed">
            دروس ترم جدید را با فیلترهای هوشمند مشاهده و ثبت‌نام کنید.
          </p>
          <a href="#unit" class="btn-glow w-full py-3 rounded-xl bg-gradient-to-r from-blue-600 to-indigo-700 text-white font-bold shadow-lg hover:shadow-blue-500/25 transition-all block text-center">
            ورود به سیستم
          </a>
        </div>
      </div>

      <!-- Card 2 -->
      <div data-aos="zoom-in" data-aos-delay="300" class="card-hover group relative bg-white dark:bg-slate-800 rounded-3xl p-8 shadow-lg overflow-hidden">
        <div class="absolute inset-0 bg-gradient-to-br from-amber-500/5 to-orange-600/5 opacity-0 group-hover:opacity-100 transition-opacity"></div>
        <div class="relative z-10">
          <div class="w-20 h-20 mx-auto mb-6 bg-gradient-to-br from-amber-500 to-orange-600 rounded-2xl flex items-center justify-center shadow-xl group-hover:scale-110 transition-transform">
            <i class="ti ti-building text-3xl text-white"></i>
          </div>
          <h3 class="text-2xl font-bold text-amber-700 dark:text-amber-300 mb-3">خوابگاه</h3>
          <p class="text-slate-600 dark:text-slate-400 mb-6 leading-relaxed">
            اتاق دلخواه خود را با یک کلیک رزرو یا تمدید کنید.
          </p>
          <a href="#dorm" class="btn-glow w-full py-3 rounded-xl bg-gradient-to-r from-amber-500 to-orange-600 text-white font-bold shadow-lg hover:shadow-amber-500/25 transition-all block text-center">
            رزرو اتاق
          </a>
        </div>
      </div>

      <!-- Card 3 -->
      <div data-aos="zoom-in" data-aos-delay="500" class="card-hover group relative bg-white dark:bg-slate-800 rounded-3xl p-8 shadow-lg overflow-hidden">
        <div class="absolute inset-0 bg-gradient-to-br from-green-500/5 to-emerald-600/5 opacity-0 group-hover:opacity-100 transition-opacity"></div>
        <div class="relative z-10">
          <div class="w-20 h-20 mx-auto mb-6 bg-gradient-to-br from-green-500 to-emerald-600 rounded-2xl flex items-center justify-center shadow-xl group-hover:scale-110 transition-transform">
            <i class="ti ti-utensils text-3xl text-white"></i>
          </div>
          <h3 class="text-2xl font-bold text-green-700 dark:text-green-300 mb-3">رزرو غذا</h3>
          <p class="text-slate-600 dark:text-slate-400 mb-6 leading-relaxed">
            منوی هفته آینده را ببینید و غذای مورد علاقه خود را رزرو کنید.
          </p>
          <a href="#food" class="btn-glow w-full py-3 rounded-xl bg-gradient-to-r from-green-500 to-emerald-600 text-white font-bold shadow-lg hover:shadow-green-500/25 transition-all block text-center">
            انتخاب غذا
          </a>
        </div>
      </div>
    </div>
  </section>

  <!-- Unit Selection Section -->
  <section id="unit" class="py-16 bg-slate-100 dark:bg-slate-900">
    <div class="container mx-auto px-6">
      <h2 class="text-3xl font-bold text-center mb-12 text-blue-900 dark:text-blue-300">انتخاب واحد</h2>
      <div class="bg-white dark:bg-slate-800 rounded-3xl p-8 shadow-xl">
        <div class="mb-6 flex justify-between items-center">
          <input type="text" id="unit-search" placeholder="جستجو دروس..." class="p-3 rounded-xl border border-slate-300 dark:border-slate-600 bg-transparent w-1/2">
          <select id="unit-filter" class="p-3 rounded-xl border border-slate-300 dark:border-slate-600 bg-transparent">
            <option>همه گروه‌ها</option>
            <option>کامپیوتر</option>
            <option>مهندسی</option>
            <option>علوم پایه</option>
          </select>
        </div>
        <table>
          <thead>
            <tr>
              <th>نام درس</th>
              <th>واحد</th>
              <th>استاد</th>
              <th>زمان</th>
              <th>انتخاب</th>
            </tr>
          </thead>
          <tbody id="unit-table">
            <!-- Sample Rows -->
            <tr>
              <td>برنامه‌نویسی پیشرفته</td>
              <td>3</td>
              <td>دکتر احمدی</td>
              <td>شنبه 10-12</td>
              <td><input type="checkbox" class="unit-checkbox"></td>
            </tr>
            <tr>
              <td>ریاضیات گسسته</td>
              <td>3</td>
              <td>دکتر رضایی</td>
              <td>یکشنبه 14-16</td>
              <td><input type="checkbox" class="unit-checkbox"></td>
            </tr>
            <tr>
              <td>فیزیک 1</td>
              <td>4</td>
              <td>دکتر محمدی</td>
              <td>دوشنبه 8-10</td>
              <td><input type="checkbox" class="unit-checkbox"></td>
            </tr>
            <!-- Add more rows as needed -->
          </tbody>
        </table>
        <div class="mt-8 flex justify-end">
          <button onclick="saveUnits()" class="btn-glow px-8 py-3 rounded-xl bg-blue-600 text-white font-bold shadow-lg">ثبت انتخاب واحد</button>
        </div>
      </div>
    </div>
  </section>

  <!-- Dorm Reservation Section -->
  <section id="dorm" class="py-16">
    <div class="container mx-auto px-6">
      <h2 class="text-3xl font-bold text-center mb-12 text-amber-700 dark:text-amber-300">رزرو خوابگاه</h2>
      <div class="bg-white dark:bg-slate-800 rounded-3xl p-8 shadow-xl">
        <form id="dorm-form">
          <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-6">
            <div>
              <label class="block mb-2 font-medium">نوع اتاق</label>
              <select class="w-full p-3 rounded-xl border border-slate-300 dark:border-slate-600 bg-transparent">
                <option>تک نفره</option>
                <option>دو نفره</option>
                <option>سه نفره</option>
              </select>
            </div>
            <div>
              <label class="block mb-2 font-medium">ساختمان</label>
              <select class="w-full p-3 rounded-xl border border-slate-300 dark:border-slate-600 bg-transparent">
                <option>ساختمان A</option>
                <option>ساختمان B</option>
                <option>ساختمان C</option>
              </select>
            </div>
            <div>
              <label class="block mb-2 font-medium">طبقه</label>
              <input type="number" min="1" max="10" class="w-full p-3 rounded-xl border border-slate-300 dark:border-slate-600 bg-transparent">
            </div>
            <div>
              <label class="block mb-2 font-medium">شماره اتاق</label>
              <input type="text" placeholder="مثال: 101" class="w-full p-3 rounded-xl border border-slate-300 dark:border-slate-600 bg-transparent">
            </div>
          </div>
          <div class="flex justify-end">
            <button type="button" onclick="saveDorm()" class="btn-glow px-8 py-3 rounded-xl bg-amber-600 text-white font-bold shadow-lg">رزرو اتاق</button>
          </div>
        </form>
      </div>
    </div>
  </section>

  <!-- Food Reservation Section -->
  <section id="food" class="py-16 bg-slate-100 dark:bg-slate-900">
    <div class="container mx-auto px-6">
      <h2 class="text-3xl font-bold text-center mb-12 text-green-700 dark:text-green-300">رزرو غذا</h2>
      <div class="bg-white dark:bg-slate-800 rounded-3xl p-8 shadow-xl">
        <div class="mb-6">
          <label class="block mb-2 font-medium">انتخاب روز</label>
          <select id="food-day" class="w-full p-3 rounded-xl border border-slate-300 dark:border-slate-600 bg-transparent">
            <option>شنبه</option>
            <option>یکشنبه</option>
            <option>دوشنبه</option>
            <option>سه‌شنبه</option>
            <option>چهارشنبه</option>
            <option>پنجشنبه</option>
            <option>جمعه</option>
          </select>
        </div>
        <table>
          <thead>
            <tr>
              <th>نام غذا</th>
              <th>قیمت (تومان)</th>
              <th>انتخاب</th>
            </tr>
          </thead>
          <tbody id="food-table">
            <!-- Sample Rows -->
            <tr>
              <td>چلو کباب</td>
              <td>۵۰,۰۰۰</td>
              <td><input type="checkbox" class="food-checkbox"></td>
            </tr>
            <tr>
              <td>زرشک پلو با مرغ</td>
              <td>۴۰,۰۰۰</td>
              <td><input type="checkbox" class="food-checkbox"></td>
            </tr>
            <tr>
              <td>قورمه سبزی</td>
              <td>۳۵,۰۰۰</td>
              <td><input type="checkbox" class="food-checkbox"></td>
            </tr>
            <!-- Add more rows as needed -->
          </tbody>
        </table>
        <div class="mt-8 flex justify-end">
          <button onclick="saveFood()" class="btn-glow px-8 py-3 rounded-xl bg-green-600 text-white font-bold shadow-lg">ثبت رزرو غذا</button>
        </div>
      </div>
    </div>
  </section>

  <!-- Footer -->
  <footer class="mt-24 py-12 bg-gradient-to-t from-slate-900 to-slate-800 text-white">
    <div class="container mx-auto px-6 text-center">
      <div class="flex justify-center items-center space-x-reverse space-x-6 mb-6">
        <i class="ti ti-copyright text-xl"></i>
        <p class="text-lg">۲۰۲۵ سامانه دانشگاهی | طراحی شده با <span class="text-red-500">♥</span> توسط تیم فناوری اطلاعات</p>
      </div>
      <div class="flex justify-center space-x-reverse space-x-4 text-2xl">
        <a href="#" class="hover:text-yellow-400 transition"><i class="ti ti-brand-instagram"></i></a>
        <a href="#" class="hover:text-yellow-400 transition"><i class="ti ti-brand-telegram"></i></a>
        <a href="#" class="hover:text-yellow-400 transition"><i class="ti ti-brand-linkedin"></i></a>
      </div>
    </div>
  </footer>

  <!-- Scripts -->
  <script src="https://unpkg.com/aos@2.3.1/dist/aos.js"></script>
  <script>
    // Initialize AOS
    AOS.init({ duration: 800, once: true });

    // Loader
    window.addEventListener('load', () => {
      setTimeout(() => {
        document.getElementById('loader').style.opacity = '0';
        setTimeout(() => {
          document.getElementById('loader').style.display = 'none';
        }, 500);
      }, 800);
    });

    // Theme Toggle
    const themeToggle = document.getElementById('theme-toggle');
    const html = document.documentElement;
    const moonIcon = '<i class="ti ti-moon text-xl"></i>';
    const sunIcon = '<i class="ti ti-sun text-xl"></i>';

    themeToggle.addEventListener('click', () => {
      html.classList.toggle('dark');
      themeToggle.innerHTML = html.classList.contains('dark') ? sunIcon : moonIcon;
      localStorage.setItem('theme', html.classList.contains('dark') ? 'dark' : 'light');
    });

    // Load saved theme
    if (localStorage.getItem('theme') === 'dark') {
      html.classList.add('dark');
      themeToggle.innerHTML = sunIcon;
    }

    // Mobile Menu
    const mobileBtn = document.getElementById('mobile-menu-btn');
    const mobileMenu = document.getElementById('mobile-menu');
    const closeMenu = document.getElementById('close-menu');

    mobileBtn.addEventListener('click', () => mobileMenu.classList.remove('hidden'));
    closeMenu.addEventListener('click', () => mobileMenu.classList.add('hidden'));
    mobileMenu.addEventListener('click', (e) => {
      if (e.target === mobileMenu) mobileMenu.classList.add('hidden');
    });

    // Smooth Scroll for Links
    document.querySelectorAll('a[href^="#"]').forEach(anchor => {
      anchor.addEventListener('click', function (e) {
        e.preventDefault();
        document.querySelector(this.getAttribute('href')).scrollIntoView({
          behavior: 'smooth'
        });
        mobileMenu.classList.add('hidden');
      });
    });

    // Unit Selection Functions
    function saveUnits() {
      const selected = Array.from(document.querySelectorAll('.unit-checkbox:checked')).map(cb => cb.parentElement.parentElement.children[0].textContent);
      localStorage.setItem('selectedUnits', JSON.stringify(selected));
      alert('انتخاب واحد ثبت شد: ' + selected.join(', '));
    }

    // Dorm Reservation Functions
    function saveDorm() {
      const formData = new FormData(document.getElementById('dorm-form'));
      const data = Object.fromEntries(formData);
      localStorage.setItem('dormReservation', JSON.stringify(data));
      alert('رزرو خوابگاه ثبت شد!');
    }

    // Food Reservation Functions
    function saveFood() {
      const day = document.getElementById('food-day').value;
      const selected = Array.from(document.querySelectorAll('.food-checkbox:checked')).map(cb => cb.parentElement.parentElement.children[0].textContent);
      localStorage.setItem('foodReservation_' + day, JSON.stringify(selected));
      alert('رزرو غذا برای ' + day + ' ثبت شد: ' + selected.join(', '));
    }

    // Logout Function
    function logout() {
      localStorage.clear();
      alert('شما از سیستم خارج شدید.');
      window.location.href = '#home';
    }

    // Search and Filter for Units
    const unitSearch = document.getElementById('unit-search');
    const unitFilter = document.getElementById('unit-filter');
    const unitTable = document.getElementById('unit-table');

    function filterUnits() {
      const searchText = unitSearch.value.toLowerCase();
      const filterGroup = unitFilter.value;
      Array.from(unitTable.rows).forEach(row => {
        const name = row.cells[0].textContent.toLowerCase();
        const group = row.getAttribute('data-group') || ''; // Add data-group to rows if needed
        row.style.display = (name.includes(searchText) && (filterGroup === 'همه گروه‌ها' || group === filterGroup)) ? '' : 'none';
      });
    }

    unitSearch.addEventListener('input', filterUnits);
    unitFilter.addEventListener('change', filterUnits);
  </script>
</body>
</html>
```

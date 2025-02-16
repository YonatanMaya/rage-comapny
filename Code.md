import React, { useState, useEffect, createContext, useContext } from 'react';
import { BrowserRouter as Router, Routes, Route, Link, useNavigate, useParams } from 'react-router-dom';
import { motion } from 'framer-motion';
// שימוש באייקונים מ-lucide-react
import { Shield, Zap, Target, Flame, Crosshair } from 'lucide-react';
// אפשר להשתמש גם ב-emailjs-com לצורך שליחת מייל אם תרצה

/**********************************/
/*****    הגדרת קבועים      *****/
/**********************************/

// כתובת מייל לאדמין (עם הרשאות מחיקה/עריכה)
const RAGE_ADMIN_EMAIL = 'ragecompany79@gmail.com';

// הקשר (Context) עבור הזיכרונות והרשאות
const MemoriesContext = createContext();

// הוק מותאם לשימוש בערכים מהקונטקסט
function useMemories() {
  return useContext(MemoriesContext);
}

/**********************************/
/*****    קומפוננטת ספק הקשר   *****/
/**********************************/

function MemoriesProvider({ children }) {
  // כאן נשמור את רשימת הזיכרונות (מ-array של objects)
  // כדי שבפתיחה יהיה לנו גם כמה דוגמאות, נכניס כמה ערכים דמה.
  const [memories, setMemories] = useState([
    // דוגמאות מוכנות
    {
      id: 1,
      name: 'יוסי לורם',
      email: 'yossi@example.com',
      team: 'צוות 1',
      role: 'מפקד',
      eventName: 'הנסיעה לכיכר סעד',
      date: '2025-02-01',
      googleMapsLocation: '',
      memoryText: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce at semper neque.',
      aiImageRequested: false,
      uploadedImage: null,
    },
    {
      id: 2,
      name: 'משה איפסום',
      email: 'moshe@example.com',
      team: 'צוות 2',
      role: 'חוליה',
      eventName: 'לילה של הדו״צ וחדירת המחבלים בארבעה רכבים',
      date: '2025-02-02',
      googleMapsLocation: '',
      memoryText: 'Sed vehicula eros vitae orci euismod, ac dictum dui lobortis.',
      aiImageRequested: false,
      uploadedImage: null,
    },
    {
      id: 3,
      name: 'אבי כהן',
      email: 'avi@example.com',
      team: 'צוות 3',
      role: 'תותחן',
      eventName: 'ליל הרחפנים מעל 2ב',
      date: '2025-02-03',
      googleMapsLocation: '',
      memoryText: 'Aliquam faucibus lobortis quam, ut congue nisi condimentum non.',
      aiImageRequested: true,
      uploadedImage: null,
    },
    {
      id: 4,
      name: 'מאור ליפשיץ',
      email: 'maor@example.com',
      team: 'צוות ד׳',
      role: 'נהג',
      eventName: 'ערב הכניסה הקרקעית',
      date: '2025-02-04',
      googleMapsLocation: '',
      memoryText: 'Vestibulum id ligula porta felis euismod semper.',
      aiImageRequested: false,
      uploadedImage: null,
    },
    {
      id: 5,
      name: 'רון שולמן',
      email: 'ron@example.com',
      team: 'תכנון',
      role: 'מפקד',
      eventName: 'הלחימה במוצב פלסטין',
      date: '2025-02-05',
      googleMapsLocation: '',
      memoryText: 'Curabitur blandit tempus porttitor. Donec id elit non mi porta gravida.',
      aiImageRequested: false,
      uploadedImage: null,
    },
    {
      id: 6,
      name: 'דני טסט',
      email: 'dani@example.com',
      team: 'רספ״ים',
      role: 'מפל״ג',
      eventName: 'הכניסה לבית חאנון',
      date: '2025-02-06',
      googleMapsLocation: '',
      memoryText: 'Nullam quis risus eget urna mollis ornare vel eu leo. Maecenas faucibus mollis interdum.',
      aiImageRequested: false,
      uploadedImage: null,
    },
    {
      id: 7,
      name: 'ראובן זמיר',
      email: 'reuven@example.com',
      team: 'צוות 4',
      role: 'טען',
      eventName: 'הכניסה לשאטי-רימאל',
      date: '2025-02-07',
      googleMapsLocation: '',
      memoryText: 'Morbi leo risus, porta ac consectetur ac, vestibulum at eros.',
      aiImageRequested: false,
      uploadedImage: null,
    },
    {
      id: 8,
      name: 'דוד יריב',
      email: 'david@example.com',
      team: 'צוות ג׳',
      role: 'חוליה',
      eventName: 'הגעה לסבב שני בנחל עוז',
      date: '2025-02-08',
      googleMapsLocation: '',
      memoryText: 'Aenean lacinia bibendum nulla sed consectetur.',
      aiImageRequested: true,
      uploadedImage: null,
    },
    {
      id: 9,
      name: 'יואל אשכנזי',
      email: 'yoel@example.com',
      team: 'צוות 1ב',
      role: 'נהג',
      eventName: 'הגעה לסבב שלישי בנחל עוז',
      date: '2025-02-09',
      googleMapsLocation: '',
      memoryText: 'Vestibulum id ligula porta felis euismod semper. Lorem ipsum dolor sit amet.',
      aiImageRequested: false,
      uploadedImage: null,
    },
  ]);

  // ננהל מצב עבור אדמין
  const [isAdmin, setIsAdmin] = useState(false);

  // הוספת זיכרון חדש
  const addMemory = (memory) => {
    setMemories((prev) => [...prev, memory]);
  };

  // עריכת זיכרון קיים
  const editMemory = (updatedMemory) => {
    setMemories((prev) => {
      return prev.map((mem) => (mem.id === updatedMemory.id ? updatedMemory : mem));
    });
  };

  // מחיקת זיכרון
  const deleteMemory = (id) => {
    setMemories((prev) => prev.filter((mem) => mem.id !== id));
  };

  // בדיקה האם מייל = מייל אדמין
  const loginAsAdmin = (email) => {
    if (email === RAGE_ADMIN_EMAIL) {
      setIsAdmin(true);
      alert('התחברת כאדמין בהצלחה');
    } else {
      alert('אין לך הרשאות');
    }
  };

  const value = {
    memories,
    addMemory,
    editMemory,
    deleteMemory,
    isAdmin,
    loginAsAdmin,
  };

  return (
    <MemoriesContext.Provider value={value}>
      {children}
    </MemoriesContext.Provider>
  );
}

/*******************************************/
/*****    פונקציה לשליפת אייקון      *****/
/*******************************************/

function getIconByEventName(eventName) {
  const lower = (eventName || '').toLowerCase();
  if (lower.includes('רחפן') || lower.includes('חדירה') || lower.includes('מחבל')) {
    return <Zap className="w-6 h-6 text-white" />;
  } else if (lower.includes('חאנון') || lower.includes('בית')) {
    return <Shield className="w-6 h-6 text-white" />;
  } else if (lower.includes('עוז') || lower.includes('סבב')) {
    return <Crosshair className="w-6 h-6 text-white" />;
  } else if (lower.includes('מוצב') || lower.includes('לחימה')) {
    return <Flame className="w-6 h-6 text-white" />;
  } else {
    return <Target className="w-6 h-6 text-white" />; // ברירת מחדל
  }
}

/*************************************************/
/*****    קיבוץ זיכרונות לפי eventName      *****/
/*************************************************/

function groupMemoriesByEvent(memories) {
  // אובייקט: eventName -> array of memories
  const groups = {};

  memories.forEach((mem) => {
    const event = mem.eventName?.trim() || 'ללא אירוע';
    if (!groups[event]) {
      groups[event] = [];
    }
    groups[event].push(mem);
  });

  // כעת נמיר את זה לרשימה של אובייקטים { eventName, date, memories }
  // נבחר date כראשון/הכי מוקדם בין הזיכרונות, לשם הסדר.

  const result = Object.keys(groups).map((eventName) => {
    const mems = groups[eventName];
    // לחפש תאריך
    let dateCandidate = mems[0].date || '';
    // אפשר לבחור מינימום תאריך
    mems.forEach((m) => {
      if (m.date && (!dateCandidate || m.date < dateCandidate)) {
        dateCandidate = m.date;
      }
    });

    return {
      eventName,
      date: dateCandidate,
      memories: mems,
    };
  });

  // נמיין לפי date
  result.sort((a, b) => {
    if (a.date && b.date) return a.date.localeCompare(b.date);
    return a.eventName.localeCompare(b.eventName);
  });

  return result;
}

/****************************************************/
/*****    עמוד התחברות נסתר לאדמין             *****/
/****************************************************/

function AdminLoginPage() {
  const { loginAsAdmin } = useMemories();
  const [emailInput, setEmailInput] = useState('');
  const navigate = useNavigate();

  const handleLogin = (e) => {
    e.preventDefault();
    loginAsAdmin(emailInput);
    navigate('/');
  };

  return (
    <div dir="rtl" className="p-6 min-h-screen bg-orange-50 text-right">
      <div className="max-w-md mx-auto bg-white shadow rounded-2xl p-6">
        <h1 className="text-2xl font-bold mb-4">מסך התחברות לאדמין</h1>
        <form onSubmit={handleLogin} className="space-y-4">
          <div>
            <label className="block mb-1 font-semibold">הכנס כתובת מייל אדמין:</label>
            <input
              type="email"
              required
              value={emailInput}
              onChange={(e) => setEmailInput(e.target.value)}
              className="border p-2 rounded w-full"
            />
          </div>
          <button
            type="submit"
            className="bg-blue-600 text-white px-4 py-2 rounded font-semibold hover:bg-blue-700"
          >
            התחבר
          </button>
        </form>
      </div>
    </div>
  );
}

/****************************************************/
/*****    כותרת ראשית (NavBar)                *****/
/****************************************************/

function NavBar() {
  return (
    <motion.nav
      dir="rtl"
      className="p-4 shadow-md flex justify-between items-center bg-orange-100"
      initial={{ y: -50, opacity: 0 }}
      animate={{ y: 0, opacity: 1 }}
    >
      <div className="flex space-x-4">
        <Link to="/" className="text-lg font-semibold hover:underline px-2">דף הבית</Link>
        <Link to="/timeline" className="text-lg font-semibold hover:underline px-2">ציר הזמן</Link>
        <Link to="/add-memory" className="text-lg font-semibold hover:underline px-2">הוסף זיכרון</Link>
        <Link to="/about" className="text-lg font-semibold hover:underline px-2">אודות</Link>
      </div>
    </motion.nav>
  );
}

/****************************************************/
/*****     עמוד הבית                           *****/
/****************************************************/

function HomePage() {
  return (
    <motion.div
      dir="rtl"
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      className="p-6 bg-orange-50 min-h-screen text-right"
    >
      <div className="bg-white rounded-2xl p-6 shadow max-w-3xl mx-auto">
        <h1 className="text-2xl font-bold mb-4">ברוכים הבאים לפרויקט תיעוד הפלוגה</h1>
        <p className="mb-2">
          בפרויקט זה נציג ציר זמן של כלל הפעולות והאירועים שהפלוגה עברה בשנה וחצי האחרונות.
          לכל לוחם יש אפשרות להוסיף זיכרון אישי שמצטרף לציר הזמן.
        </p>
        <p className="mb-2">
          אנו מקווים שכל לוחם ימצא את המקום לשתף חוויות ייחודיות, ולהעשיר את הסיפור הכולל של הפלוגה.
        </p>
        <p>
          כדי להתחיל, ניתן ללחוץ על "ציר הזמן" לצפייה בזיכרונות או "הוסף זיכרון" לשיתוף חוויה משלך.
        </p>
      </div>
    </motion.div>
  );
}

/****************************************************/
/*****     עמוד אודות                           *****/
/****************************************************/

function AboutPage() {
  return (
    <motion.div
      dir="rtl"
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      className="p-6 bg-orange-50 min-h-screen text-right"
    >
      <div className="bg-white rounded-2xl p-6 shadow max-w-3xl mx-auto">
        <h1 className="text-2xl font-bold mb-4">אודות הפרויקט</h1>
        <p className="mb-2">
          פרויקט זה נועד לשמר ולהציג את מורשת הקרב וההיסטוריה של הפלוגה באופן יומי ואירועי.
        </p>
        <p className="mb-2">
          במערכת זו ניתן להוסיף זיכרונות באופן חופשי ללא צורך בהרשמה למערכת – רק להכניס שם ומייל.
          יחד עם זאת, רק המייל של האדמין (
          <span className="font-bold">{RAGE_ADMIN_EMAIL}</span>) יכול למחוק או לערוך זיכרונות לפי צורך.
        </p>
        <p>
          בעמוד "ציר הזמן" ניתן לצפות באירועים השונים לפי תאריכים, לחפש זיכרונות,
          ולהתרשם מהחוויות השונות של לוחמי הפלוגה.
        </p>
      </div>
    </motion.div>
  );
}

/****************************************************/
/*****     ציר הזמן (אנכי)                     *****/
/****************************************************/

function TimelinePage() {
  const { memories } = useMemories();
  const [searchText, setSearchText] = useState('');
  const [searchDate, setSearchDate] = useState('');

  // מסנן לפי טקסט ותאריך
  const filtered = memories.filter((mem) => {
    const textLower = searchText.toLowerCase();
    const matchText =
      mem.memoryText.toLowerCase().includes(textLower) ||
      (mem.eventName && mem.eventName.toLowerCase().includes(textLower)) ||
      (mem.name && mem.name.toLowerCase().includes(textLower));
    const matchDate = !searchDate || (mem.date && mem.date.includes(searchDate));
    return matchText && matchDate;
  });

  // כעת נקבץ לפי שם האירוע
  const grouped = groupMemoriesByEvent(filtered);

  return (
    <motion.div
      dir="rtl"
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      className="p-6 bg-orange-50 min-h-screen text-right"
    >
      <div className="max-w-5xl mx-auto">
        <h1 className="text-2xl font-bold mb-4">ציר הזמן</h1>
        <div className="flex flex-col md:flex-row space-y-2 md:space-y-0 md:space-x-4 mb-6">
          <input
            type="text"
            placeholder="חיפוש חופשי"
            value={searchText}
            onChange={(e) => setSearchText(e.target.value)}
            className="border p-2 rounded w-full md:w-1/2"
          />
          <input
            type="text"
            placeholder="חיפוש לפי תאריך (YYYY-MM-DD)"
            value={searchDate}
            onChange={(e) => setSearchDate(e.target.value)}
            className="border p-2 rounded w-full md:w-1/2"
          />
        </div>

        {/* כאן נציג את הציר האנכי */}
        <div className="relative border-r-2 border-gray-300 ml-4">
          {grouped.map((groupItem, idx) => {
            return (
              <div
                key={groupItem.eventName}
                className="mb-8 flex flex-col items-start"
                style={{ position: 'relative' }}
              >
                {/* הנקודה/עיגול בציר */}
                <div
                  className="w-3 h-3 bg-blue-600 rounded-full absolute"
                  style={{ left: '-5px', top: '8px' }}
                ></div>

                {/* כרטיס האירוע */}
                <div
                  className="ml-8 bg-white p-4 rounded shadow w-full max-w-2xl relative"
                  style={{ marginLeft: '2rem' }}
                >
                  <div className="absolute -left-8 top-0 w-10 h-10 flex items-center justify-center rounded-full bg-blue-600 shadow">
                    {getIconByEventName(groupItem.eventName)}
                  </div>
                  <h2 className="text-xl font-bold mb-1">
                    {groupItem.eventName === 'ללא אירוע' ? 'זיכרונות כלליים' : groupItem.eventName}
                  </h2>
                  <p className="text-sm text-gray-500 mb-2">
                    {groupItem.date ? `תאריך: ${groupItem.date}` : 'ללא תאריך מוגדר'}
                  </p>
                  <p className="text-gray-700 mb-2">סה"כ {groupItem.memories.length} זיכרונות</p>

                  {/* לינק לעמוד פרטי האירוע */}
                  <Link
                    to={`/event/${encodeURIComponent(groupItem.eventName)}`}
                    className="text-blue-600 underline font-medium"
                  >
                    מעבר לאירוע
                  </Link>
                </div>
              </div>
            );
          })}
        </div>

        {grouped.length === 0 && (
          <p className="text-gray-500 mt-4">לא נמצאו תוצאות</p>
        )}
      </div>
    </motion.div>
  );
}

/****************************************************/
/*****     עמוד פרטי אירוע/כל הזיכרונות בו    *****/
/****************************************************/

function EventPage() {
  const { memories, isAdmin, deleteMemory } = useMemories();
  const { eventName } = useParams();
  const decodedEventName = decodeURIComponent(eventName || '');

  // שליפה של כל הזיכרונות השייכים לאותו אירוע
  const relevantMemories = memories.filter(
    (m) => (m.eventName?.trim() || 'ללא אירוע') === decodedEventName
  );

  if (relevantMemories.length === 0) {
    return (
      <motion.div
        dir="rtl"
        initial={{ opacity: 0 }}
        animate={{ opacity: 1 }}
        className="p-6 bg-orange-50 min-h-screen text-right"
      >
        <div className="bg-white rounded-2xl p-6 shadow max-w-3xl mx-auto">
          <h1 className="text-2xl font-bold mb-4">{decodedEventName}</h1>
          <p>אין זיכרונות עבור האירוע הזה</p>
          <Link
            to="/timeline"
            className="bg-blue-600 text-white px-4 py-2 mt-4 inline-block rounded shadow hover:bg-blue-700"
          >
            חזרה לציר הזמן
          </Link>
        </div>
      </motion.div>
    );
  }

  const handleDelete = (id) => {
    if (!isAdmin) {
      alert('אין לך הרשאה למחוק');
      return;
    }
    if (window.confirm('האם למחוק את הזיכרון?')) {
      deleteMemory(id);
    }
  };

  return (
    <motion.div
      dir="rtl"
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      className="p-6 bg-orange-50 min-h-screen text-right"
    >
      <div className="bg-white rounded-2xl p-6 shadow max-w-3xl mx-auto">
        <h1 className="text-2xl font-bold mb-4">{decodedEventName}</h1>

        {relevantMemories.map((mem) => (
          <div key={mem.id} className="p-4 my-2 bg-gray-100 rounded relative">
            <h2 className="text-lg font-semibold mb-1">זיכרון מאת: {mem.name}</h2>
            <p className="text-gray-700 mb-1">
              <span className="font-semibold">תאריך: </span>
              {mem.date || 'לא צויין'}
            </p>
            <p className="text-gray-700 mb-1">
              <span className="font-semibold">תפקיד: </span>
              {mem.role}
            </p>
            <p className="text-gray-700 mb-2">
              <span className="font-semibold">זיכרון: </span>
              {mem.memoryText}
            </p>
            {mem.uploadedImage && (
              <div className="mb-2">
                <img
                  src={mem.uploadedImage}
                  alt="Memory Upload"
                  className="max-w-xs rounded shadow"
                />
              </div>
            )}
            {isAdmin && (
              <div className="flex space-x-2 mt-2">
                <Link
                  to={`/edit-memory/${mem.id}`}
                  className="bg-yellow-500 text-white px-3 py-1 rounded"
                >
                  ערוך
                </Link>
                <button
                  onClick={() => handleDelete(mem.id)}
                  className="bg-red-500 text-white px-3 py-1 rounded"
                >
                  מחק
                </button>
              </div>
            )}
          </div>
        ))}

        <Link
          to="/timeline"
          className="bg-blue-600 text-white px-4 py-2 mt-4 inline-block rounded shadow hover:bg-blue-700"
        >
          חזרה לציר הזמן
        </Link>
      </div>
    </motion.div>
  );
}

/****************************************************/
/*****     עמוד הוספת זיכרון (ללא שינוי)      *****/
/****************************************************/

function AddMemoryPage() {
  const { addMemory } = useMemories();
  const navigate = useNavigate();

  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [team, setTeam] = useState('צוות 1');
  const [role, setRole] = useState('מפקד');
  const [eventName, setEventName] = useState('');
  const [date, setDate] = useState('');
  const [googleMapsLocation, setGoogleMapsLocation] = useState('');
  const [memoryText, setMemoryText] = useState('');
  const [aiImageRequested, setAiImageRequested] = useState(false);
  const [uploadedImage, setUploadedImage] = useState(null);

  // פונקציית שליחת מייל לאדמין כאשר נבחר AI Image (לא ממומש כרגע)
  // ניתן להשלים עם emailjs
  const handleSubmit = (e) => {
    e.preventDefault();
    const newMemory = {
      id: Date.now(),
      name,
      email,
      team,
      role,
      eventName,
      date,
      googleMapsLocation,
      memoryText,
      aiImageRequested,
      uploadedImage,
    };

    // אם הלוחם בחר יצירת תמונה ב-AI, אפשר לקרוא לפונקציה שתשלח מייל.
    // sendAiRequestEmail(newMemory);

    addMemory(newMemory);
    // איפוס
    setName('');
    setEmail('');
    setTeam('צוות 1');
    setRole('מפקד');
    setEventName('');
    setDate('');
    setGoogleMapsLocation('');
    setMemoryText('');
    setAiImageRequested(false);
    setUploadedImage(null);
    navigate('/timeline');
  };

  const handleImageUpload = (e) => {
    const file = e.target.files[0];
    if (file) {
      const reader = new FileReader();
      reader.onloadend = () => {
        setUploadedImage(reader.result);
      };
      reader.readAsDataURL(file);
    }
  };

  return (
    <motion.div
      dir="rtl"
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      className="p-6 bg-orange-50 min-h-screen text-right"
    >
      <div className="bg-white rounded-2xl p-6 shadow max-w-3xl mx-auto">
        <h1 className="text-2xl font-bold mb-4">הוסף זיכרון</h1>
        <form onSubmit={handleSubmit} className="space-y-4">
          <div>
            <label className="block mb-1 font-semibold">שם (חובה):</label>
            <input
              type="text"
              required
              value={name}
              onChange={(e) => setName(e.target.value)}
              className="border p-2 rounded w-full"
            />
          </div>
          <div>
            <label className="block mb-1 font-semibold">מייל (חובה):</label>
            <input
              type="email"
              required
              value={email}
              onChange={(e) => setEmail(e.target.value)}
              className="border p-2 rounded w-full"
            />
          </div>
          <div>
            <label className="block mb-1 font-semibold">שם הצוות:</label>
            <select
              value={team}
              onChange={(e) => setTeam(e.target.value)}
              className="border p-2 rounded w-full"
            >
              <option>צוות 1</option>
              <option>צוות 1ב</option>
              <option>צוות 1א</option>
              <option>צוות 2</option>
              <option>צוות 2ב</option>
              <option>צוות 2א</option>
              <option>צוות 3</option>
              <option>צוות 3ב</option>
              <option>צוות 3א</option>
              <option>צוות 4</option>
              <option>צוות 4ב</option>
              <option>צוות 4א</option>
              <option>צוות ג׳</option>
              <option>צוות ד׳</option>
              <option>רספ״ים</option>
              <option>תכנון</option>
              <option>חוליה</option>
              <option>אחר</option>
            </select>
          </div>
          <div>
            <label className="block mb-1 font-semibold">תפקיד:</label>
            <select
              value={role}
              onChange={(e) => setRole(e.target.value)}
              className="border p-2 rounded w-full"
            >
              <option>מפקד</option>
              <option>תותחן</option>
              <option>טען</option>
              <option>נהג</option>
              <option>חוליה</option>
              <option>מפל״ג</option>
              <option>אחר</option>
            </select>
          </div>
          <div>
            <label className="block mb-1 font-semibold">שם האירוע:</label>
            <input
              type="text"
              value={eventName}
              onChange={(e) => setEventName(e.target.value)}
              className="border p-2 rounded w-full"
            />
          </div>
          <div>
            <label className="block mb-1 font-semibold">תאריך האירוע:</label>
            <input
              type="date"
              value={date}
              onChange={(e) => setDate(e.target.value)}
              className="border p-2 rounded w-full"
            />
          </div>
          <div>
            <label className="block mb-1 font-semibold">קישור Google Maps (מיקום האירוע):</label>
            <input
              type="text"
              value={googleMapsLocation}
              onChange={(e) => setGoogleMapsLocation(e.target.value)}
              className="border p-2 rounded w-full"
            />
          </div>
          <div>
            <label className="block mb-1 font-semibold">תיאור הזיכרון:</label>
            <textarea
              value={memoryText}
              onChange={(e) => setMemoryText(e.target.value)}
              rows="5"
              className="border p-2 rounded w-full"
              placeholder="ספרו בהרחבה על החוויה..."
            />
          </div>
          <div>
            <label className="inline-flex items-center space-x-2 mb-2 font-semibold">
              <input
                type="checkbox"
                checked={aiImageRequested}
                onChange={(e) => setAiImageRequested(e.target.checked)}
                className="ml-2"
              />
              <span>אני רוצה לייצר תמונה מהזיכרון באמצעות מודל AI</span>
            </label>
            {aiImageRequested && (
              <p className="text-sm text-red-600">
                לעיתים אנו נוטים להדחיק טראומות ומראות קשים, שקלו שוב אם תרצו לחזור לרגע זה.
              </p>
            )}
          </div>
          <div>
            <label className="block mb-1 font-semibold">העלה תמונה מהזיכרון (אופציונלי):</label>
            <input
              type="file"
              onChange={handleImageUpload}
              className="border p-2 rounded w-full"
            />
            {uploadedImage && (
              <div className="mt-2">
                <img
                  src={uploadedImage}
                  alt="Preview"
                  className="max-w-xs rounded shadow"
                />
              </div>
            )}
          </div>
          <button
            type="submit"
            className="bg-blue-600 text-white px-4 py-2 rounded font-semibold hover:bg-blue-700"
          >
            הוסף זיכרון
          </button>
        </form>
      </div>
    </motion.div>
  );
}

/****************************************************/
/*****     עמוד עריכת זיכרון                 *****/
/****************************************************/

function EditMemoryPage() {
  const { memories, editMemory, isAdmin } = useMemories();
  const navigate = useNavigate();
  const [currentMemory, setCurrentMemory] = useState(null);

  const path = window.location.pathname;
  const memoryIdString = path.split('/edit-memory/')[1];
  const memoryId = parseInt(memoryIdString, 10);

  useEffect(() => {
    const mem = memories.find((m) => m.id === memoryId);
    if (!mem) {
      navigate('/timeline');
    } else {
      setCurrentMemory({ ...mem });
    }
  }, [memoryId, memories, navigate]);

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!isAdmin) {
      alert('אין לך הרשאה לערוך זיכרון זה.');
      return;
    }
    editMemory(currentMemory);
    navigate('/timeline');
  };

  if (!currentMemory) {
    return null;
  }

  const handleImageUpload = (e) => {
    const file = e.target.files[0];
    if (file) {
      const reader = new FileReader();
      reader.onloadend = () => {
        setCurrentMemory({ ...currentMemory, uploadedImage: reader.result });
      };
      reader.readAsDataURL(file);
    }
  };

  return (
    <motion.div
      dir="rtl"
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      className="p-6 bg-orange-50 min-h-screen text-right"
    >
      <div className="bg-white rounded-2xl p-6 shadow max-w-3xl mx-auto">
        <h1 className="text-2xl font-bold mb-4">ערוך זיכרון</h1>
        {!isAdmin && (
          <p className="text-red-600">אין הרשאה לערוך זיכרון זה.</p>
        )}
        {isAdmin && (
          <form onSubmit={handleSubmit} className="space-y-4">
            <div>
              <label className="block mb-1 font-semibold">שם:</label>
              <input
                type="text"
                value={currentMemory.name}
                onChange={(e) => setCurrentMemory({ ...currentMemory, name: e.target.value })}
                className="border p-2 rounded w-full"
              />
            </div>
            <div>
              <label className="block mb-1 font-semibold">מייל:</label>
              <input
                type="email"
                value={currentMemory.email}
                onChange={(e) => setCurrentMemory({ ...currentMemory, email: e.target.value })}
                className="border p-2 rounded w-full"
              />
            </div>
            <div>
              <label className="block mb-1 font-semibold">שם הצוות:</label>
              <select
                value={currentMemory.team}
                onChange={(e) => setCurrentMemory({ ...currentMemory, team: e.target.value })}
                className="border p-2 rounded w-full"
              >
                <option>צוות 1</option>
                <option>צוות 1ב</option>
                <option>צוות 1א</option>
                <option>צוות 2</option>
                <option>צוות 2ב</option>
                <option>צוות 2א</option>
                <option>צוות 3</option>
                <option>צוות 3ב</option>
                <option>צוות 3א</option>
                <option>צוות 4</option>
                <option>צוות 4ב</option>
                <option>צוות 4א</option>
                <option>צוות ג׳</option>
                <option>צוות ד׳</option>
                <option>רספ״ים</option>
                <option>תכנון</option>
                <option>חוליה</option>
                <option>אחר</option>
              </select>
            </div>
            <div>
              <label className="block mb-1 font-semibold">תפקיד:</label>
              <select
                value={currentMemory.role}
                onChange={(e) => setCurrentMemory({ ...currentMemory, role: e.target.value })}
                className="border p-2 rounded w-full"
              >
                <option>מפקד</option>
                <option>תותחן</option>
                <option>טען</option>
                <option>נהג</option>
                <option>חוליה</option>
                <option>מפל״ג</option>
                <option>אחר</option>
              </select>
            </div>
            <div>
              <label className="block mb-1 font-semibold">שם האירוע:</label>
              <input
                type="text"
                value={currentMemory.eventName}
                onChange={(e) => setCurrentMemory({ ...currentMemory, eventName: e.target.value })}
                className="border p-2 rounded w-full"
              />
            </div>
            <div>
              <label className="block mb-1 font-semibold">תאריך האירוע:</label>
              <input
                type="date"
                value={currentMemory.date}
                onChange={(e) => setCurrentMemory({ ...currentMemory, date: e.target.value })}
                className="border p-2 rounded w-full"
              />
            </div>
            <div>
              <label className="block mb-1 font-semibold">קישור Google Maps (מיקום האירוע):</label>
              <input
                type="text"
                value={currentMemory.googleMapsLocation}
                onChange={(e) => setCurrentMemory({ ...currentMemory, googleMapsLocation: e.target.value })}
                className="border p-2 rounded w-full"
              />
            </div>
            <div>
              <label className="block mb-1 font-semibold">תיאור הזיכרון:</label>
              <textarea
                rows="5"
                value={currentMemory.memoryText}
                onChange={(e) => setCurrentMemory({ ...currentMemory, memoryText: e.target.value })}
                className="border p-2 rounded w-full"
              />
            </div>
            <div>
              <label className="inline-flex items-center space-x-2 mb-2 font-semibold">
                <input
                  type="checkbox"
                  checked={currentMemory.aiImageRequested}
                  onChange={(e) =>
                    setCurrentMemory({ ...currentMemory, aiImageRequested: e.target.checked })
                  }
                  className="ml-2"
                />
                <span>אני רוצה לייצר תמונה מהזיכרון באמצעות מודל AI</span>
              </label>
              {currentMemory.aiImageRequested && (
                <p className="text-sm text-red-600">
                  לעיתים אנו נוטים להדחיק טראומות ומראות קשים, שקלו שוב אם תרצו לחזור לרגע זה.
                </p>
              )}
            </div>
            <div>
              <label className="block mb-1 font-semibold">העלה תמונה מהזיכרון (אופציונלי):</label>
              <input
                type="file"
                onChange={handleImageUpload}
                className="border p-2 rounded w-full"
              />
              {currentMemory.uploadedImage && (
                <div className="mt-2">
                  <img
                    src={currentMemory.uploadedImage}
                    alt="Preview"
                    className="max-w-xs rounded shadow"
                  />
                </div>
              )}
            </div>
            <button
              type="submit"
              className="bg-blue-600 text-white px-4 py-2 rounded font-semibold hover:bg-blue-700"
            >
              שמור שינויים
            </button>
          </form>
        )}
      </div>
    </motion.div>
  );
}

/****************************************************/
/*****     האפליקציה הראשית                  *****/
/****************************************************/

function App() {
  return (
    <MemoriesProvider>
      <Router>
        <NavBar />
        <Routes>
          <Route path="/" element={<HomePage />} />
          <Route path="/about" element={<AboutPage />} />
          <Route path="/timeline" element={<TimelinePage />} />
          <Route path="/event/:eventName" element={<EventPage />} />
          <Route path="/add-memory" element={<AddMemoryPage />} />
          <Route path="/edit-memory/:id" element={<EditMemoryPage />} />
          <Route path="/admin" element={<AdminLoginPage />} />
        </Routes>
      </Router>
    </MemoriesProvider>
  );
}

export default App;

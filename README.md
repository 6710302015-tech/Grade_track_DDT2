# Grade_track_DDT2
grade track ddt jinwara
import React, { useState, useEffect, useMemo } from 'react';
import { 
  GraduationCap, 
  Award, 
  Plus, 
  Trash2, 
  Edit3, 
  BookOpen, 
  User, 
  CheckCircle2, 
  AlertCircle,
  TrendingUp,
  Info,
  ChevronRight,
  Filter,
  Check,
  RotateCcw,
  Sliders,
  Sparkles,
  Wifi,
  Battery,
  Signal
} from 'lucide-react';

const CURRICULUM_REQUIREMENTS = {
  total: 133,
  categories: {
    general: {
      title: "หมวดศึกษาทั่วไป",
      required: 33,
      subcategories: {
        gen_thai: { title: "1.1 กลุ่มวิชาภาษาไทย", required: 3 },
        gen_foreign: { title: "1.2 กลุ่มวิชาภาษาต่างประเทศ", required: 6 },
        gen_science: { title: "1.3 กลุ่มวิชาวิทยาศาสตร์", required: 3 },
        gen_math: { title: "1.4 กลุ่มวิชาคณิตศาสตร์", required: 3 },
        gen_social: { title: "1.5 กลุ่มวิชาสังคมศาสตร์", required: 3 },
        gen_human: { title: "1.6 กลุ่มวิชามนุษยศาสตร์", required: 3 },
        gen_elective: { title: "กลุ่มศึกษาทั่วไปเลือก/อื่นๆ", required: 12 }
      }
    },
    core: {
      title: "กลุ่มวิชาแกน",
      required: 27
    },
    major: {
      title: "กลุ่มวิชาเฉพาะ",
      required: 67,
      subcategories: {
        major_required: { title: "กลุ่มวิชาชีพบังคับ", required: 52 },
        major_elective: { title: "กลุ่มวิชาชีพเลือก", required: 15 }
      }
    },
    free_elective: {
      title: "หมวดเลือกเสรี",
      required: 6
    }
  }
};

const GRADE_POINTS = {
  'A': 4.0, 'B+': 3.5, 'B': 3.0, 'C+': 2.5, 'C': 2.0, 'D+': 1.5, 'D': 1.0, 'F': 0.0,
  'W': null, 'S': null, 'U': null
};

const INITIAL_COURSES = [
  // ปี 1 เทอม 1
  { id: '1', code: 'THAI101', name: 'ภาษาไทยเพื่อการสื่อสารระดับอุดมศึกษา', credits: 3, grade: 'A', category: 'general', subcategory: 'gen_thai', year: 1, semester: '1' },
  { id: '2', code: 'ENG101', name: 'English for Academic Purposes I', credits: 3, grade: 'B+', category: 'general', subcategory: 'gen_foreign', year: 1, semester: '1' },
  { id: '3', code: 'SCI101', name: 'นวัตกรรมวิทยาศาสตร์และสิ่งแวดล้อม', credits: 3, grade: 'A', category: 'general', subcategory: 'gen_science', year: 1, semester: '1' },
  { id: '4', code: 'MATH101', name: 'แคลคูลัสสำหรับวิทยาการคอมพิวเตอร์', credits: 3, grade: 'B', category: 'general', subcategory: 'gen_math', year: 1, semester: '1' },
  { id: '5', code: 'CORE101', name: 'จริยธรรมวิชาชีพและกฎหมายไอที', credits: 3, grade: 'A', category: 'core', subcategory: '', year: 1, semester: '1' },
  
  // ปี 1 เทอม 2
  { id: '6', code: 'ENG102', name: 'English for Academic Purposes II', credits: 3, grade: 'A', category: 'general', subcategory: 'gen_foreign', year: 1, semester: '2' },
  { id: '7', code: 'SOC101', name: 'พลเมืองโลกกับความรับผิดชอบทางสังคม', credits: 3, grade: 'B+', category: 'general', subcategory: 'gen_social', year: 1, semester: '2' },
  { id: '8', code: 'HUM101', name: 'สุนทรียศาสตร์และศิลปะแห่งการดำเนินชีวิต', credits: 3, grade: 'A', category: 'general', subcategory: 'gen_human', year: 1, semester: '2' },
  { id: '9', code: 'CORE102', name: 'คณิตศาสตร์ไม่ต่อเนื่องและการวิเคราะห์', credits: 3, grade: 'C+', category: 'core', subcategory: '', year: 1, semester: '2' },
  { id: '10', code: 'MJR101', name: 'การเขียนโปรแกรมคอมพิวเตอร์พื้นฐาน', credits: 3, grade: 'B', category: 'major', subcategory: 'major_required', year: 1, semester: '2' },
  
  // ปี 2 เทอม 1
  { id: '11', code: 'CORE201', name: 'โครงสร้างข้อมูลและอัลกอริทึม', credits: 3, grade: 'A', category: 'core', subcategory: '', year: 2, semester: '1' },
  { id: '12', code: 'MJR201', name: 'ระบบการจัดการฐานข้อมูลขั้นสูง', credits: 3, grade: 'B+', category: 'major', subcategory: 'major_required', year: 2, semester: '1' },
  { id: '13', code: 'MJR202', name: 'การวิเคราะห์และออกแบบระบบเชิงวัตถุ', credits: 3, grade: 'A', category: 'major', subcategory: 'major_required', year: 2, semester: '1' },
  { id: '14', code: 'GEN201', name: 'ผู้นำกับการสื่อสารในยุคดิจิทัล', credits: 3, grade: 'A', category: 'general', subcategory: 'gen_elective', year: 2, semester: '1' }
];

export default function App() {
  const [courses, setCourses] = useState(() => {
    const saved = localStorage.getItem('gradtrack_phone_courses_jinwara');
    return saved ? JSON.parse(saved) : INITIAL_COURSES;
  });

  const [userProfile, setUserProfile] = useState(() => {
    const saved = localStorage.getItem('gradtrack_phone_profile_jinwara');
    return saved ? JSON.parse(saved) : {
      name: 'Jinwara Ruenpanich',
      studentId: '6710302015',
      department: 'สาขาเทคโนโลยีสารสนเทศและวิทยาการข้อมูล',
      advisor: 'ผศ.ดร. ณิชชา วัฒนพานิช',
      enrollmentYear: 2567,
      isOvertime: false,       
      hasRepeatedCourse: false  
    };
  });

  const [activeTab, setActiveTab] = useState('dashboard');
  const [isDarkMode, setIsDarkMode] = useState(false);
  const [notification, setNotification] = useState(null);
  
  // ระบบเวลาจำลองบนสเตตัสบาร์โทรศัพท์
  const [phoneTime, setPhoneTime] = useState('09:41');

  // ฟอร์มสำหรับการ เพิ่ม/แก้ไข รายวิชา
  const [isModalOpen, setIsModalOpen] = useState(false);
  const [editingCourse, setEditingCourse] = useState(null);
  const [formState, setFormState] = useState({
    code: '',
    name: '',
    credits: 3,
    grade: 'A',
    category: 'general',
    subcategory: 'gen_thai',
    year: 2,
    semester: '1'
  });

  // ตัวกรองตารางจัดการวิชา
  const [filterYear, setFilterYear] = useState('all');
  const [filterSem, setFilterSem] = useState('all');

  useEffect(() => {
    const updateClock = () => {
      const now = new Date();
      let hours = now.getHours().toString().padStart(2, '0');
      let minutes = now.getMinutes().toString().padStart(2, '0');
      setPhoneTime(`${hours}:${minutes}`);
    };
    updateClock();
    const interval = setInterval(updateClock, 15000);
    return () => clearInterval(interval);
  }, []);

  useEffect(() => {
    localStorage.setItem('gradtrack_phone_courses_jinwara', JSON.stringify(courses));
  }, [courses]);

  useEffect(() => {
    localStorage.setItem('gradtrack_phone_profile_jinwara', JSON.stringify(userProfile));
  }, [userProfile]);

  const showToast = (message, type = 'success') => {
    setNotification({ message, type });
    setTimeout(() => setNotification(null), 2500);
  };

  const calculationData = useMemo(() => {
    let totalCreditsEarned = 0;   
    let totalCreditsAttempted = 0;
    let totalGradePoints = 0;     
    let hasF = false;             

    const earnedSummary = {
      general: {
        total: 0,
        sub: { gen_thai: 0, gen_foreign: 0, gen_science: 0, gen_math: 0, gen_social: 0, gen_human: 0, gen_elective: 0 }
      },
      core: 0,
      major: {
        total: 0,
        sub: { major_required: 0, major_elective: 0 }
      },
      free_elective: 0
    };

    courses.forEach(c => {
      const isPassed = c.grade !== 'F' && c.grade !== 'W' && c.grade !== 'U';
      const pts = GRADE_POINTS[c.grade];

      if (c.grade === 'F') {
        hasF = true;
      }

      if (isPassed) {
        totalCreditsEarned += c.credits;

        if (c.category === 'general') {
          earnedSummary.general.total += c.credits;
          if (c.subcategory && earnedSummary.general.sub[c.subcategory] !== undefined) {
            earnedSummary.general.sub[c.subcategory] += c.credits;
          } else {
            earnedSummary.general.sub.gen_elective += c.credits;
          }
        } else if (c.category === 'core') {
          earnedSummary.core += c.credits;
        } else if (c.category === 'major') {
          earnedSummary.major.total += c.credits;
          if (c.subcategory && earnedSummary.major.sub[c.subcategory] !== undefined) {
            earnedSummary.major.sub[c.subcategory] += c.credits;
          }
        } else if (c.category === 'free_elective') {
          earnedSummary.free_elective += c.credits;
        }
      }

      if (pts !== null) {
        totalCreditsAttempted += c.credits;
        totalGradePoints += (pts * c.credits);
      }
    });

    const gpax = totalCreditsAttempted > 0 ? (totalGradePoints / totalCreditsAttempted) : 0;

    // --- ตรวจวิชาที่ยังขาดและคำนวณ Deficits รายละเอียดอย่างละเอียด ---
    const deficits = [];

    // 1. หมวดศึกษาทั่วไป
    const genSubReq = CURRICULUM_REQUIREMENTS.categories.general.subcategories;
    let genSubTotalEffective = 0;

    const genKeys = ['gen_thai', 'gen_foreign', 'gen_science', 'gen_math', 'gen_social', 'gen_human'];
    genKeys.forEach(key => {
      const earned = earnedSummary.general.sub[key];
      const req = genSubReq[key].required;
      genSubTotalEffective += Math.min(earned, req);
      
      if (earned < req) {
        deficits.push({
          category: 'general',
          label: genSubReq[key].title,
          required: req,
          earned: earned,
          missing: req - earned,
          details: `ต้องการกลุ่มนี้อย่างน้อยอีก ${req - earned} หน่วยกิต`
        });
      }
    });

    const remainingGenEdNeeded = CURRICULUM_REQUIREMENTS.categories.general.required - earnedSummary.general.total;
    if (remainingGenEdNeeded > 0) {
      deficits.push({
        category: 'general_total',
        label: 'หมวดศึกษาทั่วไป (ต้องรวมครบ 33 นก.)',
        required: CURRICULUM_REQUIREMENTS.categories.general.required,
        earned: earnedSummary.general.total,
        missing: remainingGenEdNeeded,
        details: `ยังขาดวิชาเลือกหรือกลุ่มทั่วไปอื่นอีก ${remainingGenEdNeeded} หน่วยกิต`
      });
    }

    // 2. กลุ่มวิชาแกน (27 หน่วยกิต)
    const remainingCoreNeeded = CURRICULUM_REQUIREMENTS.categories.core.required - earnedSummary.core;
    if (remainingCoreNeeded > 0) {
      deficits.push({
        category: 'core',
        label: 'กลุ่มวิชาแกน (ขั้นต่ำ 27 นก.)',
        required: CURRICULUM_REQUIREMENTS.categories.core.required,
        earned: earnedSummary.core,
        missing: remainingCoreNeeded,
        details: `ยังขาดรายวิชาแกนพื้นฐานในโครงสร้างอีก ${remainingCoreNeeded} หน่วยกิต`
      });
    }

    // 3. หมวดวิชาเฉพาะ (67 หน่วยกิต)
    // 3.1 บังคับ (52)
    const majorReqEarned = earnedSummary.major.sub.major_required;
    const majorReqRequired = CURRICULUM_REQUIREMENTS.categories.major.subcategories.major_required.required;
    if (majorReqEarned < majorReqRequired) {
      deficits.push({
        category: 'major_required',
        label: 'วิชาชีพบังคับ (52 นก.)',
        required: majorReqRequired,
        earned: majorReqEarned,
        missing: majorReqRequired - majorReqEarned,
        details: `ขาดวิชาชีพบังคับหลักในการพัฒนาอีก ${majorReqRequired - majorReqEarned} หน่วยกิต`
      });
    }
    // 3.2 เลือก (15)
    const majorEleEarned = earnedSummary.major.sub.major_elective;
    const majorEleRequired = CURRICULUM_REQUIREMENTS.categories.major.subcategories.major_elective.required;
    if (majorEleEarned < majorEleRequired) {
      deficits.push({
        category: 'major_elective',
        label: 'วิชาชีพเลือก (15 นก.)',
        required: majorEleRequired,
        earned: majorEleEarned,
        missing: majorEleRequired - majorEleEarned,
        details: `ต้องลงทะเบียนเรียนวิชาเลือกวิชาชีพเพิ่มอีก ${majorEleRequired - majorEleEarned} หน่วยกิต`
      });
    }

    // 4. หมวดเลือกเสรี (6 หน่วยกิต)
    const freeEarned = earnedSummary.free_elective;
    const freeRequired = CURRICULUM_REQUIREMENTS.categories.free_elective.required;
    if (freeEarned < freeRequired) {
      deficits.push({
        category: 'free_elective',
        label: 'หมวดวิชาเลือกเสรี (6 นก.)',
        required: freeRequired,
        earned: freeEarned,
        missing: freeRequired - freeEarned,
        details: `ยังขาดหน่วยกิตอิสระที่เลือกลงทะเบียนตามใจชอบอีก ${freeRequired - freeEarned} หน่วยกิต`
      });
    }

    let honorStatus = "ไม่มีสิทธิ์รับเกียรตินิยม";
    let honorColor = "text-stone-500 dark:text-stone-400";
    let honorBadgeBg = "bg-stone-100 dark:bg-stone-800/80";
    let honorCriteriaList = [];

    const basicEligible = !hasF && !userProfile.isOvertime && !userProfile.hasRepeatedCourse;

    if (hasF) honorCriteriaList.push("มีเกรด F อยู่ในประวัติการเรียน");
    if (userProfile.isOvertime) honorCriteriaList.push("จบการศึกษาล่าช้าเกินกว่าแผนปกติ (4 ปี)");
    if (userProfile.hasRepeatedCourse) honorCriteriaList.push("เคยขอลงทะเบียนซ้ำหรือขอแก้ตัวผลการเรียน");

    if (basicEligible) {
      if (gpax >= 3.75) {
        honorStatus = "เกียรตินิยมอันดับ 1 (First Class Honors)";
        honorColor = "text-[#A87C43] dark:text-[#D4AF37]";
        honorBadgeBg = "bg-[#FAF5EE] dark:bg-[#32291E] border border-[#E9DCC9] dark:border-[#52412B]";
      } else if (gpax >= 3.25) {
        honorStatus = "เกียรตินิยมอันดับ 2 (Second Class Honors)";
        honorColor = "text-stone-700 dark:text-[#C2A383]";
        honorBadgeBg = "bg-stone-100 dark:bg-[#2C2C2E] border border-stone-200 dark:border-stone-800";
      } else {
        honorStatus = "ปกติ (คะแนนสะสมไม่ถึงเกณฑ์)";
        honorCriteriaList.push("เกรดเฉลี่ยปัจจุบันสะสมน้อยกว่า 3.25");
      }
    } else if (gpax < 3.25) {
      honorCriteriaList.push("เกรดเฉลี่ยปัจจุบันสะสมน้อยกว่า 3.25");
    }

    return {
      gpax: gpax.toFixed(2),
      rawGpax: gpax,
      totalCreditsEarned,
      totalCreditsAttempted,
      earnedSummary,
      deficits,
      isGraduated: totalCreditsEarned >= CURRICULUM_REQUIREMENTS.total && deficits.length === 0,
      hasF,
      honorStatus,
      honorColor,
      honorBadgeBg,
      honorCriteriaList,
      basicEligible
    };
  }, [courses, userProfile]);

  const termGPAData = useMemo(() => {
    const terms = {};
    courses.forEach(c => {
      const termKey = `${c.year}/${c.semester}`;
      if (!terms[termKey]) {
        terms[termKey] = { totalPoints: 0, totalCredits: 0, count: 0, year: c.year, semester: c.semester };
      }
      const pts = GRADE_POINTS[c.grade];
      if (pts !== null) {
        terms[termKey].totalCredits += c.credits;
        terms[termKey].totalPoints += (pts * c.credits);
      }
      terms[termKey].count++;
    });

    return Object.keys(terms).map(key => {
      const t = terms[key];
      const gpa = t.totalCredits > 0 ? (t.totalPoints / t.totalCredits) : 0;
      return {
        label: `ปี ${t.year} เทอม ${t.semester}`,
        gpa: gpa.toFixed(2),
        raw: gpa,
        year: t.year,
        semester: t.semester
      };
    }).sort((a, b) => {
      if (a.year !== b.year) return a.year - b.year;
      const semA = a.semester === 'Summer' ? '3' : a.semester;
      const semB = b.semester === 'Summer' ? '3' : b.semester;
      return semA.localeCompare(semB);
    });
  }, [courses]);

  const handleSaveCourse = (e) => {
    e.preventDefault();
    if (!formState.code || !formState.name) {
      showToast('กรุณากรอกข้อมูลให้ครบถ้วน', 'error');
      return;
    }

    if (editingCourse) {
      setCourses(prev => prev.map(c => 
        c.id === editingCourse.id 
          ? { ...c, ...formState, credits: Number(formState.credits) }
          : c
      ));
      showToast('อัปเดตวิชาเรียนเรียบร้อย', 'success');
    } else {
      const newCourse = {
        id: Date.now().toString(),
        ...formState,
        credits: Number(formState.credits)
      };
      setCourses(prev => [...prev, newCourse]);
      showToast('เพิ่มวิชาเรียนเรียบร้อย', 'success');
    }
    setIsModalOpen(false);
  };

  const handleDeleteCourse = (id) => {
    setCourses(prev => prev.filter(c => c.id !== id));
    showToast('ลบวิชาเรียนเรียบร้อยแล้ว', 'success');
  };

  const handleProfileChange = (field, value) => {
    setUserProfile(prev => ({
      ...prev,
      [field]: value
    }));
  };

  const filteredCourses = useMemo(() => {
    return courses.filter(c => {
      const matchYear = filterYear === 'all' || c.year === Number(filterYear);
      const matchSem = filterSem === 'all' || c.semester === filterSem;
      return matchYear && matchSem;
    });
  }, [courses, filterYear, filterSem]);

  // คลาสและสี Quiet Luxury ของ Loro Piana
  const colors = {
    bg: isDarkMode ? 'bg-[#18181A] text-[#EDEAE4]' : 'bg-[#FAF7F2] text-[#3D3530]',
    card: isDarkMode ? 'bg-[#242426] border-[#313134]' : 'bg-white border-[#EDE5DA]',
    navBg: isDarkMode ? 'bg-[#242426]/95 border-[#313134]' : 'bg-white/95 border-[#EFEAE2]',
    primary: 'bg-[#8A674D] text-white hover:bg-[#72543D]', 
    primaryLight: 'bg-[#FAF5EE] text-[#8A674D] border-[#E9DCC9]',
    accentText: 'text-[#8A674D] dark:text-[#C5A78E]'
  };

  return (
    <div className={`min-h-screen flex items-center justify-center p-0 sm:p-6 transition-all duration-300 bg-[#EFEAE2] dark:bg-[#121213]`}>
      
      {/* 
        SMARTPHONE MOCKUP CONTAINER 
        บนหน้าจอเดสก์ท็อป จะจำลองลักษณะมือถือหรูหรา 
        แต่เมื่อเปิดในหน้าจอขนาดเล็กจริง ๆ จะแสดงผลแบบเต็มจอ (w-full h-full sm:rounded-3xl)
      */}
      <div className={`w-full max-w-md h-full sm:h-[860px] sm:rounded-[44px] overflow-hidden sm:shadow-2xl border-0 sm:border-[8px] sm:border-[#D4CBBF] dark:sm:border-[#2D2D30] relative flex flex-col justify-between transition-all ${colors.bg}`}>
        
        {/* PHONE STATUS BAR (จำลองสถานะโทรศัพท์ของ Apple/Android เพื่อความสมจริง) */}
        <div className="px-5 py-2.5 flex items-center justify-between text-xs font-semibold tracking-tight select-none opacity-80 shrink-0 z-30">
          <span>{phoneTime}</span>
          {/* Dynamic Island / Notch simulated space on Desktop only */}
          <div className="hidden sm:block w-28 h-4 bg-[#D4CBBF] dark:bg-[#2D2D30] rounded-full absolute left-1/2 -translate-x-1/2 top-2.5" />
          <div className="flex items-center gap-1.5">
            <Signal className="w-3.5 h-3.5" />
            <Wifi className="w-3.5 h-3.5" />
            <Battery className="w-4 h-4" />
          </div>
        </div>

        {/* --- APP MAIN HEADER --- */}
        <header className={`px-4 py-3 border-b flex items-center justify-between transition-all ${colors.card}`}>
          <div className="flex items-center gap-2">
            <div className="p-1.5 rounded-xl bg-[#8A674D]/10 text-[#8A674D]">
              <GraduationCap className="w-5 h-5" />
            </div>
            <div>
              <h1 className="text-sm font-extrabold tracking-tight">GradTrack</h1>
              <p className="text-[9px] opacity-65 font-mono">MYGRADPATH v2.0</p>
            </div>
          </div>

          <div className="flex items-center gap-2">
            {/* สลับ Dark Mode ภายในจำลอง */}
            <button 
              onClick={() => setIsDarkMode(!isDarkMode)} 
              className="p-1.5 rounded-xl hover:bg-stone-100 dark:hover:bg-neutral-800 transition-colors"
              aria-label="Toggle Dark Mode"
            >
              {isDarkMode ? '☀️' : '🌙'}
            </button>
          </div>
        </header>

        {/* --- SCROLLABLE PHONE PORT --- */}
        <div className="flex-grow overflow-y-auto px-4 py-4 space-y-4 pb-20">
          
          {/* USER WELCOME CARD (แถบโปรไฟล์ขนาดกะทัดรัดที่หน้าแรกที่ถูกใจ) */}
          <div className={`p-3.5 rounded-2xl border ${colors.card} shadow-sm flex items-center justify-between gap-3 relative overflow-hidden`}>
            <div className="absolute top-0 right-0 w-24 h-24 bg-[#8A674D]/5 rounded-full blur-xl pointer-events-none" />
            
            <div className="flex items-center gap-3">
              <div className="w-10 h-10 rounded-full bg-[#FAF5EE] dark:bg-stone-800 border border-[#E9DCC9] dark:border-stone-700 flex items-center justify-center font-serif font-extrabold text-[#8A674D]">
                {userProfile.name[0]}
              </div>
              <div className="min-w-0">
                <div className="flex items-center gap-1.5">
                  <h2 className="text-xs font-bold text-stone-800 dark:text-stone-100 truncate max-w-[120px]">{userProfile.name}</h2>
                  <span className="text-[8px] font-mono px-1.5 py-0.5 bg-stone-100 dark:bg-stone-800 rounded-md">67</span>
                </div>
                <p className="text-[9px] opacity-60 truncate max-w-[160px]">{userProfile.department}</p>
                <p className="text-[8px] opacity-50 truncate max-w-[160px]">ที่ปรึกษา: {userProfile.advisor}</p>
              </div>
            </div>

            <div className="text-right shrink-0">
              <span className="text-[9px] font-mono block opacity-50">STUDENT ID</span>
              <span className="text-[10px] font-bold font-mono text-[#8A674D] dark:text-[#C5A78E]">
                {userProfile.studentId}
              </span>
            </div>
          </div>

          {/* TAB 1: DASHBOARD */}
          {activeTab === 'dashboard' && (
            <div className="space-y-4 animate-in fade-in duration-200">
              
              {/* GPAX & CREDIT COUNTER */}
              <div className={`p-4 rounded-3xl border ${colors.card} shadow-sm space-y-3`}>
                <div className="flex justify-between items-start">
                  <div>
                    <h2 className="text-[11px] font-bold text-stone-500 dark:text-stone-400">เกรดเฉลี่ยสะสมรวม (GPAX)</h2>
                    <div className="flex items-baseline gap-2 mt-1">
                      <span className="text-3xl font-extrabold tracking-tight font-serif text-[#8A674D] dark:text-[#C5A78E]">
                        {calculationData.gpax}
                      </span>
                      <span className="text-xs opacity-50">/ 4.00</span>
                    </div>
                  </div>
                  
                  <div className="text-right">
                    <span className="text-[9px] font-bold px-2 py-0.5 rounded-full bg-[#FAF5EE] dark:bg-stone-800 text-[#8A674D] border border-[#E9DCC9]/40">
                      ชั้นปีที่ 2 (รหัส 67)
                    </span>
                  </div>
                </div>

                <div className="pt-3 border-t border-dashed border-stone-200 dark:border-stone-800 grid grid-cols-2 gap-3">
                  <div>
                    <span className="text-[9px] block opacity-50">หน่วยกิตสะสมที่ผ่าน</span>
                    <span className="text-xs font-bold font-serif">{calculationData.totalCreditsEarned} <span className="text-[10px] font-normal opacity-60">/ 133 นก.</span></span>
                  </div>
                  <div>
                    <span className="text-[9px] block opacity-50">วิชาที่ลงทะเบียนเรียน</span>
                    <span className="text-xs font-bold font-serif">{courses.length} <span className="text-[10px] font-normal opacity-60">รายวิชา</span></span>
                  </div>
                </div>
              </div>

              {/* คาดการณ์สิทธิ์เกียรตินิยม */}
              <div className={`p-4 rounded-3xl border ${calculationData.honorBadgeBg} space-y-3 shadow-sm`}>
                <div className="flex justify-between items-center">
                  <span className="text-[9px] font-bold uppercase tracking-wider opacity-60">สิทธิ์เกียรตินิยมคาดการณ์</span>
                  <Award className={`w-4 h-4 ${calculationData.basicEligible && calculationData.rawGpax >= 3.25 ? 'text-amber-500' : 'text-stone-400'}`} />
                </div>
                <div>
                  <h3 className={`text-xs font-extrabold leading-snug ${calculationData.honorColor}`}>
                    {calculationData.honorStatus}
                  </h3>
                  {calculationData.honorCriteriaList.length > 0 ? (
                    <div className="mt-2 space-y-1">
                      {calculationData.honorCriteriaList.map((crit, idx) => (
                        <p key={idx} className="text-[9px] text-rose-500 dark:text-rose-400 flex items-center gap-1">
                          <span className="w-1 h-1 rounded-full bg-rose-400 shrink-0" />
                          {crit}
                        </p>
                      ))}
                    </div>
                  ) : (
                    <p className="text-[9px] text-emerald-600 dark:text-emerald-400 flex items-center gap-1 mt-1.5">
                      <span className="w-1 h-1 rounded-full bg-emerald-500" />
                      ตรงตามเกณฑ์เสริมทั้งหมด (ไม่มี F / ไม่มีรีเกรด / จบใน 4 ปี)
                    </p>
                  )}
                </div>
              </div>

              {/* วิเคราะห์วิชาและหน่วยกิตที่ยังขาด (Graduation Deficits Analyzer) */}
              <div className={`p-4 rounded-3xl border ${colors.card} shadow-sm space-y-4`}>
                <div className="flex justify-between items-center">
                  <div className="flex items-center gap-1.5">
                    <AlertCircle className="w-4 h-4 text-amber-500" />
                    <h3 className="text-[11px] font-bold uppercase tracking-wider opacity-75">
                      วิชา/หน่วยกิตที่ยังขาด ({calculationData.deficits.length} หมวดหลัก)
                    </h3>
                  </div>
                  <span className="text-[9px] opacity-50">เกณฑ์ 133 นก.</span>
                </div>

                {calculationData.deficits.length === 0 ? (
                  <div className="bg-emerald-50 dark:bg-emerald-950/20 p-3 rounded-2xl text-center border border-emerald-100 dark:border-emerald-900/30">
                    <span className="text-xs text-emerald-700 dark:text-emerald-400 font-bold block">🎉 สมบูรณ์แบบ 100%</span>
                    <span className="text-[9px] text-emerald-600/85 block mt-0.5">คุณลงเรียนครบทุกโครงสร้างวิชาหลักสูตรแล้ว</span>
                  </div>
                ) : (
                  <div className="space-y-2 max-h-[220px] overflow-y-auto pr-1">
                    {calculationData.deficits.map((def, idx) => (
                      <div 
                        key={idx} 
                        className="p-2.5 rounded-2xl bg-stone-50 dark:bg-neutral-800/40 border border-stone-200/40 dark:border-stone-800 flex flex-col gap-1"
                      >
                        <div className="flex justify-between items-center">
                          <span className="text-[10px] font-bold text-stone-800 dark:text-stone-200 truncate">{def.label}</span>
                          <span className="text-[8px] font-bold text-rose-500 font-mono bg-rose-50 dark:bg-rose-950/30 px-2 py-0.5 rounded-full">
                            ขาด {def.missing} นก.
                          </span>
                        </div>
                        <p className="text-[9px] opacity-70 leading-snug">{def.details}</p>
                        
                        <div className="w-full bg-stone-200/60 dark:bg-neutral-800 h-1 rounded-full overflow-hidden mt-1">
                          <div 
                            className="h-full bg-[#8A674D] rounded-full"
                            style={{ width: `${(def.earned / def.required) * 100}%` }}
                          />
                        </div>
                      </div>
                    ))}
                  </div>
                )}
              </div>

              {/* กราฟแนวโน้มเกรดสะสมรายเทอม */}
              <div className={`p-4 rounded-3xl border ${colors.card} shadow-sm space-y-3`}>
                <h3 className="text-[11px] font-bold uppercase tracking-wider opacity-65 flex items-center gap-1.5">
                  <TrendingUp className="w-3.5 h-3.5 text-[#8A674D]" />
                  GPA รายภาคการศึกษา (Trends)
                </h3>
                
                {termGPAData.length === 0 ? (
                  <p className="text-[10px] text-center opacity-50 py-3">ยังไม่มีข้อมูลวิชาเรียน</p>
                ) : (
                  <div className="space-y-2">
                    <div className="flex items-end justify-between gap-1 h-20 pt-4 border-b border-stone-100 dark:border-stone-800">
                      {termGPAData.map((t, idx) => {
                        const heightPercent = (t.raw / 4) * 100;
                        return (
                          <div key={idx} className="flex-1 flex flex-col items-center gap-1 group">
                            <span className="text-[8px] font-bold font-mono opacity-80">{t.gpa}</span>
                            <div 
                              className="w-full rounded-t-sm bg-[#8A674D] dark:bg-[#C5A78E] opacity-75 hover:opacity-100 transition-all cursor-pointer"
                              style={{ height: `${Math.max(10, heightPercent)}%` }}
                              title={`GPA ${t.gpa}`}
                            />
                            <span className="text-[8px] opacity-60 text-center truncate w-full">{t.semester === 'Summer' ? 'S' : `${t.year}/${t.semester}`}</span>
                          </div>
                        );
                      })}
                    </div>
                  </div>
                )}
              </div>

            </div>
          )}

          {/* TAB 2: CURRICULUM */}
          {activeTab === 'curriculum' && (
            <div className="space-y-4 animate-in fade-in duration-200">
              <div className="flex items-center justify-between">
                <h2 className="text-xs font-bold uppercase tracking-wider opacity-70">โครงสร้างวิชาหลักสูตร 133 นก.</h2>
                <span className="text-xs font-mono font-bold text-[#8A674D]">
                  {calculationData.totalCreditsEarned} / 133
                </span>
              </div>

              {/* หมวดศึกษาทั่วไป */}
              <div className={`p-3.5 rounded-2xl border ${colors.card} shadow-sm space-y-2.5`}>
                <div className="flex justify-between items-center text-xs font-bold">
                  <span>1. ศึกษาทั่วไป (33 นก.)</span>
                  <span className="font-mono">{calculationData.earnedSummary.general.total}/33</span>
                </div>
                <div className="w-full h-1.5 bg-stone-100 dark:bg-neutral-800 rounded-full overflow-hidden">
                  <div className="h-full bg-[#8A674D] rounded-full" style={{ width: `${Math.min(100, (calculationData.earnedSummary.general.total / 33) * 100)}%` }} />
                </div>
                
                <div className="grid grid-cols-1 gap-1.5 pt-2 border-t border-stone-100 dark:border-stone-800 text-[10px]">
                  {Object.entries(CURRICULUM_REQUIREMENTS.categories.general.subcategories).map(([key, info]) => {
                    const earned = calculationData.earnedSummary.general.sub[key] || 0;
                    return (
                      <div key={key} className="flex justify-between items-center py-1 px-1.5 rounded-lg bg-stone-50/50 dark:bg-neutral-800/20">
                        <span className="opacity-75">{info.title}</span>
                        <span className="font-mono font-bold">{earned} / {info.required} นก.</span>
                      </div>
                    );
                  })}
                </div>
              </div>

              {/* หมวดวิชาแกน */}
              <div className={`p-3.5 rounded-2xl border ${colors.card} shadow-sm space-y-2`}>
                <div className="flex justify-between items-center text-xs font-bold">
                  <span>2. กลุ่มวิชาแกน (27 นก.)</span>
                  <span className="font-mono">{calculationData.earnedSummary.core}/27</span>
                </div>
                <div className="w-full h-1.5 bg-stone-100 dark:bg-neutral-800 rounded-full overflow-hidden">
                  <div className="h-full bg-[#8A674D] rounded-full" style={{ width: `${Math.min(100, (calculationData.earnedSummary.core / 27) * 100)}%` }} />
                </div>
              </div>

              {/* หมวดวิชาเฉพาะ */}
              <div className={`p-3.5 rounded-2xl border ${colors.card} shadow-sm space-y-2.5`}>
                <div className="flex justify-between items-center text-xs font-bold">
                  <span>3. หมวดวิชาเฉพาะ (67 นก.)</span>
                  <span className="font-mono">{calculationData.earnedSummary.major.total}/67</span>
                </div>
                <div className="w-full h-1.5 bg-stone-100 dark:bg-neutral-800 rounded-full overflow-hidden">
                  <div className="h-full bg-[#8A674D] rounded-full" style={{ width: `${Math.min(100, (calculationData.earnedSummary.major.total / 67) * 100)}%` }} />
                </div>

                <div className="grid grid-cols-1 gap-1.5 pt-2 border-t border-stone-100 dark:border-stone-800 text-[10px]">
                  {Object.entries(CURRICULUM_REQUIREMENTS.categories.major.subcategories).map(([key, info]) => {
                    const earned = calculationData.earnedSummary.major.sub[key] || 0;
                    return (
                      <div key={key} className="flex justify-between items-center py-1 px-1.5 rounded-lg bg-stone-50/50 dark:bg-neutral-800/20">
                        <span className="opacity-75">{info.title}</span>
                        <span className="font-mono font-bold">{earned} / {info.required} นก.</span>
                      </div>
                    );
                  })}
                </div>
              </div>

              {/* เลือกเสรี */}
              <div className={`p-3.5 rounded-2xl border ${colors.card} shadow-sm space-y-2`}>
                <div className="flex justify-between items-center text-xs font-bold">
                  <span>4. หมวดเลือกเสรี (6 นก.)</span>
                  <span className="font-mono">{calculationData.earnedSummary.free_elective}/6</span>
                </div>
                <div className="w-full h-1.5 bg-stone-100 dark:bg-neutral-800 rounded-full overflow-hidden">
                  <div className="h-full bg-[#8A674D] rounded-full" style={{ width: `${Math.min(100, (calculationData.earnedSummary.free_elective / 6) * 100)}%` }} />
                </div>
              </div>

            </div>
          )}

          {/* TAB 3: COURSE MANAGER */}
          {activeTab === 'courses' && (
            <div className="space-y-4 animate-in fade-in duration-200">
              <div className="flex justify-between items-center">
                <div>
                  <h2 className="text-xs font-bold uppercase tracking-wider opacity-70">จัดการเกรดรายวิชา</h2>
                  <p className="text-[10px] opacity-50">แสดงผล {filteredCourses.length} รายการเรียน</p>
                </div>
                
                <button 
                  onClick={handleOpenAddModal}
                  className="flex items-center gap-1 text-[11px] font-bold bg-[#8A674D] hover:bg-[#72543D] text-white px-3 py-1.5 rounded-xl transition-all shadow-sm"
                >
                  <Plus className="w-3 h-3" />
                  เพิ่มวิชา
                </button>
              </div>

              {/* ฟิลเตอร์คัดเลือก */}
              <div className="grid grid-cols-2 gap-2 p-1.5 bg-stone-100/50 dark:bg-neutral-800/40 rounded-xl border border-stone-200/40 dark:border-stone-800">
                <select 
                  value={filterYear} 
                  onChange={(e) => setFilterYear(e.target.value)}
                  className="text-[10px] bg-white dark:bg-neutral-800 border-0 rounded-lg py-1 px-1.5 outline-none font-medium"
                >
                  <option value="all">ทุกปีการศึกษา</option>
                  <option value="1">ปี 1</option>
                  <option value="2">ปี 2</option>
                  <option value="3">ปี 3</option>
                  <option value="4">ปี 4</option>
                </select>

                <select 
                  value={filterSem} 
                  onChange={(e) => setFilterSem(e.target.value)}
                  className="text-[10px] bg-white dark:bg-neutral-800 border-0 rounded-lg py-1 px-1.5 outline-none font-medium"
                >
                  <option value="all">ทุกเทอม</option>
                  <option value="1">เทอม 1</option>
                  <option value="2">เทอม 2</option>
                  <option value="Summer">ซัมเมอร์</option>
                </select>
              </div>

              {/* ตารางแบบการ์ดโมบายล์เพื่อแตะง่าย */}
              <div className="space-y-2.5">
                {filteredCourses.map(course => (
                  <div 
                    key={course.id}
                    className={`p-3 rounded-2xl border ${colors.card} shadow-sm flex items-center justify-between gap-2.5 hover:border-[#8A674D]/45 transition-all`}
                  >
                    <div className="min-w-0 flex-grow">
                      <div className="flex items-center gap-1.5 flex-wrap">
                        <span className="text-[8px] font-extrabold font-mono text-[#8A674D] dark:text-[#C5A78E] bg-[#FAF5EE] dark:bg-stone-800 px-1.5 py-0.5 rounded-md">
                          {course.code}
                        </span>
                        <span className="text-[8px] opacity-65 font-medium">
                          ปี {course.year}/{course.semester} · {course.credits} นก.
                        </span>
                      </div>
                      <h4 className="text-[11px] font-bold truncate mt-1 text-stone-800 dark:text-stone-200 leading-snug">
                        {course.name}
                      </h4>
                    </div>

                    <div className="flex items-center gap-2">
                      <span className={`w-7 h-7 rounded-full flex items-center justify-center font-serif text-[10px] font-extrabold ${
                        course.grade === 'A' ? 'bg-emerald-50 text-emerald-700 dark:bg-emerald-950/20 dark:text-emerald-400' :
                        course.grade === 'B+' || course.grade === 'B' ? 'bg-cyan-50 text-cyan-700 dark:bg-cyan-950/20 dark:text-cyan-400' :
                        course.grade === 'C+' || course.grade === 'C' ? 'bg-amber-50 text-amber-700 dark:bg-amber-950/20 dark:text-amber-400' :
                        course.grade === 'F' ? 'bg-rose-50 text-rose-700 dark:bg-rose-950/20 dark:text-rose-400' : 'bg-stone-50 text-stone-700'
                      }`}>
                        {course.grade}
                      </span>

                      <div className="flex items-center">
                        <button 
                          onClick={() => handleOpenEditModal(course)}
                          className="p-1 text-stone-500 hover:text-[#8A674D]"
                        >
                          <Edit3 className="w-3.5 h-3.5" />
                        </button>
                        <button 
                          onClick={() => handleDeleteCourse(course.id)}
                          className="p-1 text-stone-400 hover:text-rose-500"
                        >
                          <Trash2 className="w-3.5 h-3.5" />
                        </button>
                      </div>
                    </div>

                  </div>
                ))}
              </div>

            </div>
          )}

          {/* TAB 4: PROFILE */}
          {activeTab === 'profile' && (
            <div className="space-y-4 animate-in fade-in duration-200">
              <h2 className="text-xs font-bold uppercase tracking-wider opacity-70">ปรับแต่งประวัติและเงื่อนไขการเรียน</h2>
              
              <div className={`p-4 rounded-3xl border ${colors.card} shadow-sm space-y-3.5`}>
                
                {/* แก้ไขชื่อผู้ใช้ */}
                <div>
                  <label className="block text-[10px] font-bold opacity-75 mb-1">ชื่อ - นามสกุลภาษาอังกฤษ</label>
                  <input 
                    type="text"
                    className="w-full text-xs bg-stone-50 dark:bg-neutral-800 border border-stone-200 dark:border-neutral-700 p-2.5 rounded-xl outline-none"
                    value={userProfile.name}
                    onChange={(e) => handleProfileChange('name', e.target.value)}
                  />
                </div>

                {/* แก้ไขรหัสวิทยาลัย */}
                <div>
                  <label className="block text-[10px] font-bold opacity-75 mb-1">รหัสนักศึกษา (10 หลัก)</label>
                  <input 
                    type="text"
                    className="w-full text-xs font-mono bg-stone-50 dark:bg-neutral-800 border border-stone-200 dark:border-neutral-700 p-2.5 rounded-xl outline-none"
                    value={userProfile.studentId}
                    onChange={(e) => handleProfileChange('studentId', e.target.value)}
                  />
                </div>

                {/* หลักสูตรการเรียน */}
                <div>
                  <label className="block text-[10px] font-bold opacity-75 mb-1">หลักสูตร / สาขาวิชา</label>
                  <input 
                    type="text"
                    className="w-full text-xs bg-stone-50 dark:bg-neutral-800 border border-stone-200 dark:border-neutral-700 p-2.5 rounded-xl outline-none"
                    value={userProfile.department}
                    onChange={(e) => handleProfileChange('department', e.target.value)}
                  />
                </div>

                {/* อาจารย์ที่ปรึกษา */}
                <div>
                  <label className="block text-[10px] font-bold opacity-75 mb-1">อาจารย์ที่ปรึกษา</label>
                  <input 
                    type="text"
                    className="w-full text-xs bg-stone-50 dark:bg-neutral-800 border border-stone-200 dark:border-neutral-700 p-2.5 rounded-xl outline-none"
                    value={userProfile.advisor}
                    onChange={(e) => handleProfileChange('advisor', e.target.value)}
                  />
                </div>

              </div>

              {/* ข้อจำกัดเกียรตินิยม */}
              <div className={`p-4 rounded-3xl border ${colors.card} shadow-sm space-y-3.5`}>
                <h3 className="text-[10px] font-bold uppercase tracking-wider opacity-60">เงื่อนไขผลการเรียนเสริม</h3>
                
                <div className="space-y-3">
                  <div className="flex items-center justify-between">
                    <div>
                      <span className="text-xs font-bold block leading-snug">จบหลักสูตรเกินเวลา (เกิน 4 ปี)</span>
                      <span className="text-[9px] opacity-60 block">หากเรียนช้าเกิน 4 ปี จะไม่มีสิทธิ์ชิงเกียรตินิยม</span>
                    </div>
                    <button 
                      onClick={() => handleProfileChange('isOvertime', !userProfile.isOvertime)}
                      className={`w-10 h-5.5 rounded-full transition-colors relative focus:outline-none shrink-0 ${userProfile.isOvertime ? 'bg-amber-600' : 'bg-stone-300 dark:bg-neutral-700'}`}
                    >
                      <span className={`absolute top-0.5 left-0.5 bg-white w-4.5 h-4.5 rounded-full transition-transform ${userProfile.isOvertime ? 'translate-x-4.5' : ''}`} />
                    </button>
                  </div>

                  <div className="flex items-center justify-between border-t border-stone-100 dark:border-stone-800 pt-3">
                    <div>
                      <span className="text-xs font-bold block leading-snug">เคยถอน/ลงทะเบียนเรียนซ้ำ (รีเกรด)</span>
                      <span className="text-[9px] opacity-60 block">หากเคยแก้ตัวเกรด จะทำให้ขาดสิทธิ์เกียรตินิยม</span>
                    </div>
                    <button 
                      onClick={() => handleProfileChange('hasRepeatedCourse', !userProfile.hasRepeatedCourse)}
                      className={`w-10 h-5.5 rounded-full transition-colors relative focus:outline-none shrink-0 ${userProfile.hasRepeatedCourse ? 'bg-amber-600' : 'bg-stone-300 dark:bg-neutral-700'}`}
                    >
                      <span className={`absolute top-0.5 left-0.5 bg-white w-4.5 h-4.5 rounded-full transition-transform ${userProfile.hasRepeatedCourse ? 'translate-x-4.5' : ''}`} />
                    </button>
                  </div>
                </div>

              </div>

            </div>
          )}

        </div>

        {/* --- BOTTOM MOBILE NAVIGATION BAR --- */}
        <nav className={`absolute bottom-0 left-0 right-0 z-40 border-t backdrop-blur-md transition-colors duration-300 pb-safe-bottom ${colors.navBg}`}>
          <div className="flex justify-around items-center h-16 px-1">
            
            <button 
              onClick={() => setActiveTab('dashboard')}
              className={`flex flex-col items-center justify-center flex-1 h-full gap-1 transition-all ${
                activeTab === 'dashboard' ? colors.accentText : 'opacity-40'
              }`}
            >
              <TrendingUp className="w-5 h-5" />
              <span className="text-[9px] font-bold">แดชบอร์ด</span>
            </button>

            <button 
              onClick={() => setActiveTab('curriculum')}
              className={`flex flex-col items-center justify-center flex-1 h-full gap-1 transition-all ${
                activeTab === 'curriculum' ? colors.accentText : 'opacity-40'
              }`}
            >
              <BookOpen className="w-5 h-5" />
              <span className="text-[9px] font-bold">วิชาที่ขาด</span>
            </button>

            <button 
              onClick={() => setActiveTab('courses')}
              className={`flex flex-col items-center justify-center flex-1 h-full gap-1 transition-all ${
                activeTab === 'courses' ? colors.accentText : 'opacity-40'
              }`}
            >
              <Sliders className="w-5 h-5" />
              <span className="text-[9px] font-bold">เกรดวิชา</span>
            </button>

            <button 
              onClick={() => setActiveTab('profile')}
              className={`flex flex-col items-center justify-center flex-1 h-full gap-1 transition-all ${
                activeTab === 'profile' ? colors.accentText : 'opacity-40'
              }`}
            >
              <User className="w-5 h-5" />
              <span className="text-[9px] font-bold">โปรไฟล์</span>
            </button>

          </div>
        </nav>

        {/* --- OVERLAY MODAL: SLIDE-UP FORM --- */}
        {isModalOpen && (
          <div className="absolute inset-0 z-50 flex items-end justify-center bg-black/40 backdrop-blur-xs">
            <div className={`w-full rounded-t-[32px] p-5 shadow-2xl ${colors.card} border-t animate-in slide-in-from-bottom duration-150 max-h-[90%] overflow-y-auto`}>
              
              <div className="flex justify-between items-center mb-4">
                <h3 className="text-xs font-bold uppercase tracking-wider opacity-70">
                  {editingCourse ? 'แก้ไขเกรดวิชาเรียน' : 'ลงทะเบียนวิชาเรียนเพิ่ม'}
                </h3>
                <button 
                  onClick={() => setIsModalOpen(false)}
                  className="w-6 h-6 rounded-full flex items-center justify-center bg-stone-100 dark:bg-stone-800 text-stone-500 hover:text-stone-700"
                >
                  ✕
                </button>
              </div>

              <form onSubmit={handleSaveCourse} className="space-y-3">
                
                <div className="grid grid-cols-3 gap-2">
                  <div>
                    <label className="block text-[9px] font-bold opacity-75 mb-1">รหัสวิชา</label>
                    <input 
                      type="text" required placeholder="ENG101"
                      className="w-full text-xs font-mono bg-stone-50 dark:bg-neutral-800 border border-stone-200 dark:border-neutral-700 p-2 rounded-lg outline-none"
                      value={formState.code}
                      onChange={(e) => setFormState(prev => ({ ...prev, code: e.target.value.toUpperCase() }))}
                    />
                  </div>
                  <div className="col-span-2">
                    <label className="block text-[9px] font-bold opacity-75 mb-1">ชื่อวิชาเรียน</label>
                    <input 
                      type="text" required placeholder="เช่น English I"
                      className="w-full text-xs bg-stone-50 dark:bg-neutral-800 border border-stone-200 dark:border-neutral-700 p-2 rounded-lg outline-none"
                      value={formState.name}
                      onChange={(e) => setFormState(prev => ({ ...prev, name: e.target.value }))}
                    />
                  </div>
                </div>

                <div className="grid grid-cols-2 gap-2">
                  <div>
                    <label className="block text-[9px] font-bold opacity-75 mb-1">หน่วยกิต</label>
                    <select 
                      className="w-full text-xs bg-stone-50 dark:bg-neutral-800 border border-stone-200 dark:border-neutral-700 p-2 rounded-lg outline-none"
                      value={formState.credits}
                      onChange={(e) => setFormState(prev => ({ ...prev, credits: Number(e.target.value) }))}
                    >
                      <option value={1}>1 นก.</option>
                      <option value={2}>2 นก.</option>
                      <option value={3}>3 นก.</option>
                      <option value={4}>4 นก.</option>
                      <option value={6}>6 นก.</option>
                    </select>
                  </div>
                  <div>
                    <label className="block text-[9px] font-bold opacity-75 mb-1">ผลเกรด</label>
                    <select 
                      className="w-full text-xs font-bold font-mono bg-stone-50 dark:bg-neutral-800 border border-stone-200 dark:border-neutral-700 p-2 rounded-lg outline-none"
                      value={formState.grade}
                      onChange={(e) => setFormState(prev => ({ ...prev, grade: e.target.value }))}
                    >
                      <option value="A">A (4.00)</option>
                      <option value="B+">B+ (3.50)</option>
                      <option value="B">B (3.00)</option>
                      <option value="C+">C+ (2.50)</option>
                      <option value="C">C (2.00)</option>
                      <option value="D+">D+ (1.50)</option>
                      <option value="D">D (1.00)</option>
                      <option value="F">F (0.00)</option>
                      <option value="W">W</option>
                      <option value="S">S</option>
                      <option value="U">U</option>
                    </select>
                  </div>
                </div>

                <div>
                  <label className="block text-[9px] font-bold opacity-75 mb-1">หมวดวิชาหลัก</label>
                  <select 
                    className="w-full text-xs bg-stone-50 dark:bg-neutral-800 border border-stone-200 dark:border-neutral-700 p-2 rounded-lg outline-none"
                    value={formState.category}
                    onChange={(e) => {
                      const cat = e.target.value;
                      let sub = '';
                      if (cat === 'general') sub = 'gen_thai';
                      else if (cat === 'major') sub = 'major_required';
                      setFormState(prev => ({ ...prev, category: cat, subcategory: sub }));
                    }}
                  >
                    <option value="general">หมวดศึกษาทั่วไป</option>
                    <option value="core">กลุ่มวิชาแกน</option>
                    <option value="major">หมวดวิชาเฉพาะ</option>
                    <option value="free_elective">หมวดเลือกเสรี</option>
                  </select>
                </div>

                {formState.category === 'general' && (
                  <div>
                    <label className="block text-[9px] font-bold opacity-75 mb-1">กลุ่มย่อย (ศึกษาทั่วไป)</label>
                    <select 
                      className="w-full text-[9px] bg-stone-50 dark:bg-neutral-800 border border-stone-200 dark:border-neutral-700 p-1.5 rounded-lg outline-none"
                      value={formState.subcategory}
                      onChange={(e) => setFormState(prev => ({ ...prev, subcategory: e.target.value }))}
                    >
                      <option value="gen_thai">1.1 กลุ่มภาษาไทย (3 นก.)</option>
                      <option value="gen_foreign">1.2 กลุ่มภาษาต่างประเทศ (6 นก.)</option>
                      <option value="gen_science">1.3 กลุ่มวิทยาศาสตร์ (3 นก.)</option>
                      <option value="gen_math">1.4 กลุ่มคณิตศาสตร์ (3 นก.)</option>
                      <option value="gen_social">1.5 กลุ่มสังคมศาสตร์ (3 นก.)</option>
                      <option value="gen_human">1.6 กลุ่มมนุษยศาสตร์ (3 นก.)</option>
                      <option value="gen_elective">วิชาเลือกศึกษาทั่วไปอื่นๆ</option>
                    </select>
                  </div>
                )}

                {formState.category === 'major' && (
                  <div>
                    <label className="block text-[9px] font-bold opacity-75 mb-1">กลุ่มย่อย (วิชาเฉพาะ)</label>
                    <select 
                      className="w-full text-[9px] bg-stone-50 dark:bg-neutral-800 border border-stone-200 dark:border-neutral-700 p-1.5 rounded-lg outline-none"
                      value={formState.subcategory}
                      onChange={(e) => setFormState(prev => ({ ...prev, subcategory: e.target.value }))}
                    >
                      <option value="major_required">วิชาชีพบังคับ (52 นก.)</option>
                      <option value="major_elective">วิชาชีพเลือก (15 นก.)</option>
                    </select>
                  </div>
                )}

                <div className="grid grid-cols-2 gap-2">
                  <div>
                    <label className="block text-[9px] font-bold opacity-75 mb-1">ชั้นปี</label>
                    <select 
                      className="w-full text-xs bg-stone-50 dark:bg-neutral-800 border border-stone-200 dark:border-neutral-700 p-2 rounded-lg outline-none"
                      value={formState.year}
                      onChange={(e) => setFormState(prev => ({ ...prev, year: Number(e.target.value) }))}
                    >
                      <option value={1}>ปี 1</option>
                      <option value={2}>ปี 2</option>
                      <option value={3}>ปี 3</option>
                      <option value={4}>ปี 4</option>
                    </select>
                  </div>
                  <div>
                    <label className="block text-[9px] font-bold opacity-75 mb-1">ภาคการศึกษา</label>
                    <select 
                      className="w-full text-xs bg-stone-50 dark:bg-neutral-800 border border-stone-200 dark:border-neutral-700 p-2 rounded-lg outline-none"
                      value={formState.semester}
                      onChange={(e) => setFormState(prev => ({ ...prev, semester: e.target.value }))}
                    >
                      <option value="1">ภาคเรียนที่ 1</option>
                      <option value="2">ภาคเรียนที่ 2</option>
                      <option value="Summer">ซัมเมอร์</option>
                    </select>
                  </div>
                </div>

                <div className="flex gap-2.5 pt-2">
                  <button 
                    type="button" onClick={() => setIsModalOpen(false)}
                    className="flex-1 py-2 rounded-xl border border-stone-200 dark:border-neutral-700 text-xs font-semibold hover:bg-stone-50 text-stone-500"
                  >
                    ยกเลิก
                  </button>
                  <button 
                    type="submit"
                    className="flex-1 py-2 rounded-xl text-xs font-semibold bg-[#8A674D] text-white hover:bg-[#72543D]"
                  >
                    บันทึกข้อมูล
                  </button>
                </div>

              </form>
            </div>
          </div>
        )}

      </div>

    </div>
  );
}

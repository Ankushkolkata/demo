import React, { useState, useEffect, useMemo } from 'react';
import { 
  Radar, RadarChart, PolarGrid, PolarAngleAxis, PolarRadiusAxis, 
  ResponsiveContainer, Tooltip as RechartsTooltip, Cell, Legend
} from 'recharts';
import { 
  ChevronRight, ChevronLeft, Save, Send, LayoutDashboard, 
  Settings, User, Building, ClipboardList, CheckCircle2, 
  BrainCircuit, BarChart3, TrendingUp, Info, Database,
  ShieldCheck, Users, Lightbulb, Microscope, FileText, Loader2,
  History, Trash2, ExternalLink, Tag, Briefcase, Activity,
  AlertCircle, RefreshCw
} from 'lucide-react';
import { initializeApp } from 'firebase/app';
import { getAuth, signInWithCustomToken, signInAnonymously, onAuthStateChanged } from 'firebase/auth';
import { getFirestore, doc, setDoc, getDoc, collection, addDoc, getDocs } from 'firebase/firestore';

// =============================================================================
// 1. DATA CONFIGURATION
// =============================================================================

const OPTION_SETS = {
  maturity: [
    "L1: Initial/Manual - Minimal automation, reactive processes, and siloed operations.",
    "L2: Defined - Documented processes with basic automation tools and standard interfaces.",
    "L3: Integrated - Cross-domain automation, intent-based modeling, and proactive operations.",
    "L4: Optimized - Data-driven closed-loop control with AI-assisted decision making.",
    "L5: Autonomous - Self-configuring, self-healing, and self-optimizing system intelligence."
  ],
  readiness: [
    "L1: No Awareness - No formal strategy or infrastructure in place.",
    "L2: Exploring - Pilot projects and initial awareness across leadership.",
    "L3: Functional - Established frameworks and foundational AI toolsets.",
    "L4: Strategic - Enterprise-wide scaling and data-driven cultural integration.",
    "L5: AI-First - Innovative leadership with pervasive AI-driven operational excellence."
  ]
};

const AN_QUESTION_REGISTRY = [
  // --- RAN DOMAIN ---
  { domain: "RAN", operationSubDomain: "Configuration Management", subTask: "Site Integration", internalTag: "Deployment", phase: "Execution", text: "Maturity of automated cell site configuration and neighbor relation integration?", options: "maturity" },
  { domain: "RAN", operationSubDomain: "Fault Management", subTask: "Alarm Correlation", internalTag: "Assurance", phase: "Analysis", text: "Capability of the system to correlate RAN alarms to root physical site issues automatically?", options: "maturity" },
  { domain: "RAN", operationSubDomain: "Performance Management", subTask: "Optimization", internalTag: "Radio Quality", phase: "Decision", text: "Autonomy in adjusting tilt, power, and frequency parameters based on traffic heatmaps?", options: "maturity" },
  { domain: "RAN", operationSubDomain: "Incident Management", subTask: "Auto-Ticketing", internalTag: "Ops Efficiency", phase: "Execution", text: "Level of automated work order generation for hardware-related RAN site failures?", options: "maturity" },
  { domain: "RAN", operationSubDomain: "Customer Experience Management", subTask: "User Perceived Quality", internalTag: "CX", phase: "Awareness", text: "Ability to map RAN performance degradation to specific high-value customer churn risk?", options: "maturity" },
  { domain: "RAN", operationSubDomain: "Problem Management", subTask: "Trend Analysis", internalTag: "Stability", phase: "Analysis", text: "Automated identification of repetitive RAN interference patterns across geographical clusters?", options: "maturity" },
  // --- CORE NETWORK DOMAIN ---
  { domain: "Core Network", operationSubDomain: "Configuration Management", subTask: "Network Slicing", internalTag: "Provisioning", phase: "Intent", text: "Maturity of intent-based orchestration for core network slice instantiation?", options: "maturity" },
  { domain: "Core Network", operationSubDomain: "Fault Management", subTask: "VNF Healing", internalTag: "Virtualization", phase: "Execution", text: "Level of automated scaling and healing for Core VNFs/CNFs during process failure?", options: "maturity" },
  { domain: "Core Network", operationSubDomain: "Performance Management", subTask: "Capacity Balancing", internalTag: "Resource Mgmt", phase: "Decision", text: "Autonomy in dynamic core user-plane redirection during localized signaling congestion?", options: "maturity" },
  { domain: "Core Network", operationSubDomain: "Incident Management", subTask: "Service Restoration", internalTag: "Assurance", phase: "Analysis", text: "Automated analysis and prioritization of core incidents based on service-level impact?", options: "maturity" },
  { domain: "Core Network", operationSubDomain: "Customer Experience Management", subTask: "Service Latency", internalTag: "Quality of Service", phase: "Awareness", text: "Real-time visibility of core-side latency impacts on specific premium application segments?", options: "maturity" },
  { domain: "Core Network", operationSubDomain: "Problem Management", subTask: "Root Cause Investigation", internalTag: "Network Design", phase: "Decision", text: "System capability to propose architectural changes based on long-term core bottleneck analysis?", options: "maturity" }
];

const generateReadinessRegistry = () => {
  const categories = ["AI Strategy", "Tools & Infrastructure", "Data", "Governance", "Talent & Culture"];
  const subCats = ["Operations", "Platform", "Sovereignty", "Policy", "Mindset"];
  const phases = ["Awareness", "Analysis", "Decision", "Intent", "Execution"];
  let reg = [];
  categories.forEach(cat => {
    subCats.forEach(sub => {
      for (let i = 1; i <= 5; i++) {
        reg.push({ domain: cat, operationSubDomain: sub, subTask: `Requirement Q${i}`, internalTag: "Strategic", phase: phases[i-1], text: `Evaluate organizational AI maturity for ${cat} - ${sub} - Key Requirement ${i}.`, options: "readiness" });
      }
    });
  });
  return reg;
};

// =============================================================================
// 2. MODEL BUILDER
// =============================================================================

const buildModel = (id, title, shortTitle, description, registry) => {
  const domainsMap = new Map();
  registry.forEach(q => {
    if (!domainsMap.has(q.domain)) domainsMap.set(q.domain, new Map());
    const subDomainMap = domainsMap.get(q.domain);
    if (!subDomainMap.has(q.operationSubDomain)) subDomainMap.set(q.operationSubDomain, []);
    subDomainMap.get(q.operationSubDomain).push({ text: q.text, subTask: q.subTask, category: q.internalTag, phase: q.phase, options: OPTION_SETS[q.options] });
  });
  const categories = Array.from(domainsMap.entries()).map(([domainName, subDomains]) => ({
    name: domainName, subcategories: Array.from(subDomains.entries()).map(([subName, questions]) => ({ name: subName, questions }))
  }));
  return { id, title, shortTitle, description, categories };
};

const AN_MATURITY_MODEL = buildModel('tmf-an-maturity', 'Autonomous Networks Maturity Assessment', 'AN Maturity', 'TMF-aligned maturity assessment covering RAN and Core operational domains.', AN_QUESTION_REGISTRY);
const AI_READINESS_MODEL = buildModel('ai-readiness', 'AN Readiness Assessment', 'AI Readiness', 'Enterprise-wide AI capability and infrastructure readiness evaluation.', generateReadinessRegistry());

// =============================================================================
// 3. CORE UI COMPONENTS
// =============================================================================

const APP_ID = typeof __app_id !== 'undefined' ? __app_id : 'autonomous-360-v4';
const FIREBASE_CONFIG = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : null;
const apiKey = ""; 

const MarkdownRenderer = ({ content }) => {
  if (!content) return null;
  return (
    <div className="space-y-4">
      {content.split('\n').map((line, idx) => {
        if (line.startsWith('###')) return <h3 key={idx} className="text-2xl font-bold text-indigo-400 mt-8 mb-4 border-b border-white/10 pb-2">{line.replace('###', '').trim()}</h3>;
        if (line.includes('**')) {
          const parts = line.split('**');
          return <p key={idx} className="text-slate-300">{parts.map((p, i) => i % 2 === 1 ? <strong key={i} className="text-white font-bold">{p}</strong> : p)}</p>;
        }
        if (line.trim().startsWith('-')) return <div key={idx} className="flex gap-3 text-slate-300 ml-4"><span className="text-indigo-500 font-bold">•</span><span>{line.replace('-', '').trim()}</span></div>;
        return line.trim() === '' ? <div key={idx} className="h-2"></div> : <p key={idx} className="text-slate-300 leading-relaxed">{line}</p>;
      })}
    </div>
  );
};

const ScoreMeter = ({ score }) => {
  const percentage = Math.min(Math.max(score / 5, 0), 1);
  const color = score < 2.5 ? '#f43f5e' : score < 4 ? '#f59e0b' : '#10b981';

  return (
    <div className="flex flex-col items-center justify-center relative w-full h-48 overflow-hidden">
      <svg width="240" height="130" viewBox="0 0 240 130" className="mt-8 overflow-visible">
        <path d="M 20 120 A 100 100 0 0 1 220 120" fill="none" stroke="#f1f5f9" strokeWidth="24" strokeLinecap="round" />
        <path 
          d="M 20 120 A 100 100 0 0 1 220 120" 
          fill="none" stroke={color} strokeWidth="24" strokeLinecap="round"
          strokeDasharray="314.159" strokeDashoffset={314.159 * (1 - percentage)}
          className="transition-all duration-1000 ease-out"
        />
        {[0, 1, 2, 3, 4, 5].map((val, i) => {
          const angle = (i * 36) - 180;
          const rad = angle * Math.PI / 180;
          const tx = 120 + 115 * Math.cos(rad);
          const ty = 120 + 115 * Math.sin(rad);
          return <text key={i} x={tx} y={ty} fontSize="10" fontWeight="bold" fill="#94a3b8" textAnchor="middle" alignmentBaseline="middle">{val}</text>;
        })}
      </svg>
      <div className="absolute bottom-4 text-center">
        <span className="text-5xl font-black text-slate-800 tabular-nums leading-none">{score.toFixed(1)}</span>
        <p className="text-[10px] font-bold text-slate-400 uppercase tracking-widest mt-2">Overall Maturity Index</p>
      </div>
    </div>
  );
};

const ProgressBar = ({ current, total }) => (
  <div className="w-full bg-slate-100 rounded-full h-2 mb-6 overflow-hidden">
    <div className="bg-indigo-600 h-2 transition-all duration-700 ease-in-out" style={{ width: `${(current / total) * 100}%` }}></div>
  </div>
);

// =============================================================================
// 4. MAIN APP COMPONENT
// =============================================================================

export default function App() {
  const [view, setView] = useState('landing');
  const [activeModel, setActiveModel] = useState(null);
  const [user, setUser] = useState(null);
  const [db, setDb] = useState(null);
  const [isSubmitting, setIsSubmitting] = useState(false);
  const [submissions, setSubmissions] = useState([]);
  const [loadingHistory, setLoadingHistory] = useState(false);
  const [metadata, setMetadata] = useState({ projectName: '', clientName: '', assessorName: '' });
  const [assessmentStates, setAssessmentStates] = useState({
    'tmf-an-maturity': { currentIndex: 0, answers: {}, results: null, aiAnalysis: null },
    'ai-readiness': { currentIndex: 0, answers: {}, results: null, aiAnalysis: null }
  });

  useEffect(() => {
    if (FIREBASE_CONFIG) {
      const app = initializeApp(FIREBASE_CONFIG);
      const auth = getAuth(app);
      setDb(getFirestore(app));
      const initAuth = async () => {
        if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
          await signInWithCustomToken(auth, __initial_auth_token);
        } else {
          await signInAnonymously(auth);
        }
      };
      initAuth();
      return onAuthStateChanged(auth, setUser);
    }
  }, []);

  const pagedSubcategories = useMemo(() => {
    if (!activeModel) return [];
    const pages = [];
    let qIdx = 0;
    const sortedCategories = [...activeModel.categories].sort((a, b) => a.name === "RAN" ? -1 : b.name === "RAN" ? 1 : 0);
    sortedCategories.forEach(cat => {
      cat.subcategories.forEach(sub => {
        const subQs = sub.questions.map(q => ({ ...q, globalId: `q_${qIdx++}` }));
        pages.push({ categoryName: cat.name, subcategoryName: sub.name, questions: subQs });
      });
    });
    return pages;
  }, [activeModel]);

  const currentAssessment = activeModel ? assessmentStates[activeModel.id] : null;

  const handleAction = async (type, payload) => {
    if (type === 'START') { setActiveModel(payload); setView('setup'); }
    if (type === 'LOAD_HISTORY') {
      setLoadingHistory(true);
      try {
        const col = collection(db, 'artifacts', APP_ID, 'users', user.uid, 'submissions');
        const snap = await getDocs(col);
        setSubmissions(snap.docs.map(d => ({ id: d.id, ...d.data() })).sort((a, b) => new Date(b.timestamp) - new Date(a.timestamp)));
      } catch (e) { console.error(e); }
      setLoadingHistory(false);
      setView('history');
    }
  };

  const saveState = async (updates) => {
    const newState = { ...assessmentStates[activeModel.id], ...updates };
    setAssessmentStates(prev => ({ ...prev, [activeModel.id]: newState }));
    if (user && db) await setDoc(doc(db, 'artifacts', APP_ID, 'users', user.uid, 'data', activeModel.id), { metadata, state: newState }, { merge: true });
  };

  const generateAIAnalysis = async (results) => {
    const systemPrompt = "You are a professional Telecommunications Strategy Consultant. Your task is to provide a structured, strategic maturity report in Markdown based on Autonomous Networks assessment data. Use headers (###), bolding, and bullet points.";
    const userQuery = `Analyze results for ${activeModel.title}:
    - Project: ${metadata.projectName}
    - Overall Score: ${results.overall.toFixed(2)}/5
    - Domain Performance: ${results.domainAverages.map(d => `${d.name}: ${d.score.toFixed(2)}`).join(', ')}
    - Sub-domain Data: ${JSON.stringify(results.radarData)}

    Provide:
    ### 1. Current State Synthesis
    ### 2. Strategic Gap Analysis
    ### 3. Critical Recommendations (6-month roadmap)
    ### 4. Innovation Path (Level 5 Vision)`;

    const fetchWithRetry = async (retryCount = 0) => {
      try {
        const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            contents: [{ parts: [{ text: userQuery }] }],
            systemInstruction: { parts: [{ text: systemPrompt }] }
          })
        });
        if (!response.ok) throw new Error(`API ${response.status}`);
        const data = await response.json();
        return data.candidates?.[0]?.content?.parts?.[0]?.text;
      } catch (err) {
        if (retryCount < 4) {
          await new Promise(r => setTimeout(r, Math.pow(2, retryCount) * 1000));
          return fetchWithRetry(retryCount + 1);
        }
        throw err;
      }
    };

    try {
      return await fetchWithRetry();
    } catch (e) {
      return "### Analysis Unavailable\n\nThe AI synthesis model encountered a temporary connectivity issue. Please refer to the heatmap and radar charts below for manual maturity gap identification.";
    }
  };

  const handleSubmit = async () => {
    setIsSubmitting(true);
    const answers = currentAssessment.answers;
    const domainScores = {};
    const subDomainMap = {};

    pagedSubcategories.forEach(page => {
      page.questions.forEach(q => {
        const s = answers[q.globalId]?.score || 0;
        if (!domainScores[page.categoryName]) domainScores[page.categoryName] = [];
        domainScores[page.categoryName].push(s);
        if (!subDomainMap[page.subcategoryName]) subDomainMap[page.subcategoryName] = {};
        if (!subDomainMap[page.subcategoryName][page.categoryName]) subDomainMap[page.subcategoryName][page.categoryName] = [];
        subDomainMap[page.subcategoryName][page.categoryName].push(s);
      });
    });

    const domainAverages = Object.keys(domainScores).map(name => ({
      name, score: domainScores[name].reduce((a, b) => a + b, 0) / domainScores[name].length
    }));

    const radarData = Object.keys(subDomainMap).map(subName => {
      const entry = { subject: subName };
      Object.keys(subDomainMap[subName]).forEach(dName => {
        const vals = subDomainMap[subName][dName];
        entry[dName] = vals.reduce((a, b) => a + b, 0) / vals.length;
      });
      return entry;
    });

    const overall = domainAverages.reduce((a, b) => a + b.score, 0) / domainAverages.length;
    const results = { domainAverages, radarData, overall };

    // Explicitly await AI before submission completion
    const aiAnalysis = await generateAIAnalysis(results);

    const subData = { metadata, modelId: activeModel.id, modelTitle: activeModel.title, results, aiAnalysis, timestamp: new Date().toISOString(), answers };
    if (user && db) await addDoc(collection(db, 'artifacts', APP_ID, 'users', user.uid, 'submissions'), subData);

    await saveState({ results, aiAnalysis });
    setIsSubmitting(false);
    setView('dashboard');
  };

  const loadFromHistory = (sub) => {
    const model = sub.modelId === 'ai-readiness' ? AI_READINESS_MODEL : AN_MATURITY_MODEL;
    setActiveModel(model);
    setMetadata(sub.metadata);
    setAssessmentStates(prev => ({
      ...prev,
      [model.id]: { 
        ...prev[model.id], 
        results: sub.results, 
        aiAnalysis: sub.aiAnalysis, // Correct mapping here
        answers: sub.answers 
      }
    }));
    setView('dashboard');
  };

  // --- Views ---

  if (view === 'landing') return (
    <div className="max-w-6xl mx-auto px-6 py-20 animate-in fade-in duration-700">
      <div className="text-center mb-20">
        <div className="flex justify-center mb-6"><div className="p-4 bg-indigo-600 rounded-3xl shadow-xl shadow-indigo-100"><BrainCircuit className="w-12 h-12 text-white" /></div></div>
        <h1 className="text-6xl font-black text-slate-900 tracking-tight mb-4">Autonomous<span className="text-indigo-600">360</span></h1>
        <p className="text-xl text-slate-500 max-w-2xl mx-auto font-medium">Precision Assessments for TMF Maturity & AI Readiness.</p>
        <button onClick={() => handleAction('LOAD_HISTORY')} className="mt-12 px-8 py-4 bg-white border border-slate-200 rounded-2xl font-bold text-slate-700 hover:shadow-xl transition-all flex items-center gap-2 mx-auto">
          <History className="w-5 h-5 text-indigo-500" /> Assessment History
        </button>
      </div>
      <div className="grid grid-cols-1 md:grid-cols-2 gap-8">
        {[AN_MATURITY_MODEL, AI_READINESS_MODEL].map(m => (
          <div key={m.id} onClick={() => handleAction('START', m)} className="group bg-white p-10 rounded-[2.5rem] border border-slate-100 shadow-sm hover:shadow-2xl transition-all cursor-pointer relative overflow-hidden">
            <div className="absolute top-0 right-0 p-8 opacity-5 group-hover:scale-125 transition-all"><Microscope className="w-24 h-24" /></div>
            <h3 className="text-3xl font-black text-slate-800 mb-4">{m.title}</h3>
            <p className="text-slate-500 text-sm leading-relaxed">{m.description}</p>
            <div className="mt-8 flex items-center text-indigo-600 font-bold group-hover:gap-4 transition-all">Start Assessment <ChevronRight className="ml-2 w-5 h-5" /></div>
          </div>
        ))}
      </div>
    </div>
  );

  if (view === 'history') return (
    <div className="max-w-5xl mx-auto px-6 py-12">
      <div className="flex items-center gap-4 mb-12">
        <button onClick={() => setView('landing')} className="p-2 bg-slate-100 rounded-lg hover:bg-slate-200 transition-colors"><ChevronLeft /></button>
        <h1 className="text-4xl font-black text-slate-900">History</h1>
      </div>
      {loadingHistory ? <div className="text-center py-40"><Loader2 className="animate-spin mx-auto text-indigo-600" /></div> : (
        <div className="grid gap-4">
          {submissions.map(s => (
            <div key={s.id} className="bg-white p-6 rounded-[2rem] border flex items-center justify-between hover:shadow-lg transition-all">
              <div><h3 className="font-bold text-lg">{s.metadata.projectName}</h3><p className="text-xs text-slate-400 font-bold uppercase">{s.modelTitle} • {new Date(s.timestamp).toLocaleDateString()}</p></div>
              <button onClick={() => loadFromHistory(s)} className="px-6 py-2 bg-indigo-600 text-white rounded-xl font-bold">Open Report</button>
            </div>
          ))}
        </div>
      )}
    </div>
  );

  if (view === 'setup') return (
    <div className="max-w-xl mx-auto py-20 px-6">
      <div className="bg-white p-10 rounded-[2.5rem] shadow-xl border border-slate-100">
        <h2 className="text-3xl font-black text-slate-900 mb-8">Setup Report</h2>
        <div className="space-y-6">
          {['projectName', 'clientName', 'assessorName'].map(f => (
            <div key={f}>
              <label className="block text-xs font-black text-slate-400 uppercase mb-2">{f.replace(/([A-Z])/g, ' $1')}</label>
              <input type="text" className="w-full p-4 bg-slate-50 border border-slate-100 rounded-2xl outline-none focus:ring-2 focus:ring-indigo-600" value={metadata[f]} onChange={e => setMetadata({...metadata, [f]: e.target.value})} />
            </div>
          ))}
          <button disabled={!metadata.projectName || !metadata.clientName} onClick={() => setView('assessment')} className="w-full py-5 bg-indigo-600 text-white rounded-2xl font-black shadow-xl disabled:opacity-30">Begin Assessment</button>
        </div>
      </div>
    </div>
  );

  if (view === 'assessment') {
    const page = pagedSubcategories[currentAssessment.currentIndex];
    const isLast = currentAssessment.currentIndex === pagedSubcategories.length - 1;
    const answered = page.questions.every(q => currentAssessment.answers[q.globalId]?.score);

    if (isSubmitting) return <div className="max-w-xl mx-auto py-40 text-center"><Loader2 className="w-12 h-12 text-indigo-600 animate-spin mx-auto mb-6" /><h2 className="text-2xl font-black">Analyzing Maturity Data...</h2><p className="text-slate-500 mt-2 italic">Generating Strategic Qualitative Analysis...</p></div>;

    return (
      <div className="max-w-4xl mx-auto py-12 px-6">
        <ProgressBar current={currentAssessment.currentIndex + 1} total={pagedSubcategories.length} />
        <div className="bg-white rounded-[2.5rem] p-10 shadow-xl border border-slate-100">
          <div className="mb-12">
            <h2 className="text-3xl font-black text-slate-900 leading-none">{page.categoryName}</h2>
            <div className="flex gap-2 mt-4"><span className="px-2 py-0.5 bg-indigo-600 text-white rounded text-[10px] font-black uppercase tracking-tighter">Operation: {page.subcategoryName}</span></div>
          </div>
          <div className="space-y-20">
            {page.questions.map((q, idx) => (
              <div key={q.globalId} className="animate-in fade-in">
                <div className="flex flex-wrap gap-2 mb-4">
                  <span className="bg-slate-900 text-white w-6 h-6 rounded flex items-center justify-center text-xs font-bold">{idx + 1}</span>
                  <span className="text-[10px] font-black bg-indigo-50 text-indigo-600 px-2 py-0.5 rounded uppercase flex items-center gap-1"><Briefcase size={10}/> Task: {q.subTask}</span>
                  <span className="text-[10px] font-black bg-amber-50 text-amber-600 px-2 py-0.5 rounded uppercase flex items-center gap-1"><Activity size={10}/> Category: {q.phase}</span>
                </div>
                <h3 className="text-xl font-bold mb-6 text-slate-800 leading-tight">{q.text}</h3>
                <div className="space-y-2 mb-6">
                  {q.options.map((opt, i) => (
                    <button key={i} onClick={() => saveState({ answers: {...currentAssessment.answers, [q.globalId]: {score: i+1, comment: currentAssessment.answers[q.globalId]?.comment || ''}} })} className={`w-full text-left p-4 rounded-xl border-2 transition-all flex gap-3 text-sm ${currentAssessment.answers[q.globalId]?.score === i + 1 ? 'border-indigo-600 bg-indigo-50 font-bold text-indigo-900 shadow-sm' : 'border-slate-50 hover:border-indigo-100 text-slate-500'}`}>
                      <div className={`w-4 h-4 rounded-full border-2 mt-0.5 flex-shrink-0 flex items-center justify-center ${currentAssessment.answers[q.globalId]?.score === i + 1 ? 'border-indigo-600 bg-indigo-600' : 'border-slate-200'}`}>{currentAssessment.answers[q.globalId]?.score === i + 1 && <div className="w-1.5 h-1.5 bg-white rounded-full"></div>}</div>
                      {opt}
                    </button>
                  ))}
                </div>
                <textarea className="w-full p-4 bg-slate-50 rounded-xl text-sm h-24 outline-none resize-none placeholder-slate-300" placeholder="Provide justification..." value={currentAssessment.answers[q.globalId]?.comment || ''} onChange={e => saveState({ answers: {...currentAssessment.answers, [q.globalId]: {score: currentAssessment.answers[q.globalId]?.score, comment: e.target.value}} })} />
              </div>
            ))}
          </div>
          <div className="mt-16 flex justify-between pt-8 border-t border-slate-100">
            <button onClick={() => saveState({ currentIndex: currentAssessment.currentIndex - 1 })} disabled={currentAssessment.currentIndex === 0} className="px-6 py-3 font-bold text-slate-400 hover:text-slate-700 transition-colors">Previous</button>
            <button disabled={!answered} onClick={isLast ? handleSubmit : () => saveState({ currentIndex: currentAssessment.currentIndex + 1 })} className={`px-12 py-4 rounded-2xl font-black text-white shadow-xl transition-all ${isLast ? 'bg-emerald-600 hover:bg-emerald-700' : 'bg-indigo-600 hover:bg-indigo-700'}`}>{isLast ? 'Generate Report' : 'Next Sub-Domain'}</button>
          </div>
        </div>
      </div>
    );
  }

  if (view === 'dashboard') {
    const res = currentAssessment.results;
    const aiAnalysis = currentAssessment.aiAnalysis; // Separate from res to ensure correct logic

    return (
      <div className="max-w-7xl mx-auto px-6 py-12 animate-in fade-in">
        <div className="flex justify-between items-end mb-12">
          <div><h1 className="text-4xl font-black text-slate-900 leading-none mb-4">{activeModel.shortTitle} Report</h1><p className="text-slate-500 font-medium">Project: {metadata.projectName} • Client: {metadata.clientName}</p></div>
          <div className="flex gap-4"><button onClick={() => window.print()} className="px-6 py-3 bg-white border font-bold rounded-xl shadow-sm hover:shadow-md transition-all">Export Report</button><button onClick={() => setView('landing')} className="px-6 py-3 bg-slate-900 text-white font-bold rounded-xl flex items-center gap-2 hover:bg-black transition-all">New Assessment</button></div>
        </div>

        <div className="grid grid-cols-1 lg:grid-cols-3 gap-8 mb-8">
          <div className="bg-white p-10 rounded-[3rem] border flex flex-col items-center justify-center text-center shadow-sm">
            <h3 className="text-xs font-black uppercase tracking-widest text-slate-400 mb-8">Overall Maturity Index</h3>
            <ScoreMeter score={res.overall} />
            <div className="mt-10 grid grid-cols-2 gap-4 w-full">
              <div className="bg-slate-50 p-4 rounded-2xl"><p className="text-[10px] font-black uppercase text-slate-400">Current Status</p><p className="font-bold text-slate-700">{res.overall >= 3 ? 'Functional' : 'Limited'}</p></div>
              <div className="bg-indigo-50 p-4 rounded-2xl text-indigo-900"><p className="text-[10px] font-black uppercase text-indigo-400">Target Level</p><p className="font-bold">L 5.0</p></div>
            </div>
          </div>

          <div className="lg:col-span-2 bg-white p-10 rounded-[3rem] border shadow-sm">
            <h3 className="font-bold mb-6 flex items-center gap-2 text-slate-800"><BarChart3 className="text-indigo-500" /> Operational Domain Maturity</h3>
            <div className="h-[350px]">
              <ResponsiveContainer width="100%" height="100%">
                <RadarChart cx="50%" cy="50%" outerRadius="80%" data={res.radarData}>
                  <PolarGrid stroke="#f1f5f9" />
                  <PolarAngleAxis dataKey="subject" tick={{fill: '#64748b', fontSize: 10, fontWeight: 700}} />
                  <PolarRadiusAxis angle={30} domain={[0, 5]} />
                  {res.domainAverages.map((d, i) => (
                    <Radar key={i} name={d.name} dataKey={d.name} stroke={i === 0 ? "#4f46e5" : "#10b981"} fill={i === 0 ? "#4f46e5" : "#10b981"} fillOpacity={0.4} strokeWidth={2} />
                  ))}
                  <RechartsTooltip contentStyle={{borderRadius: '12px', border: 'none', boxShadow: '0 10px 15px -3px rgba(0,0,0,0.1)'}} />
                  <Legend />
                </RadarChart>
              </ResponsiveContainer>
            </div>
          </div>
        </div>

        <div className="bg-white p-10 rounded-[3rem] border shadow-sm mb-8 overflow-hidden">
          <h3 className="font-bold mb-8 flex items-center gap-2 font-black text-slate-800 tracking-tight"><LayoutDashboard className="text-indigo-500" /> Operation Sub-Domain Heatmap</h3>
          <div className="overflow-x-auto">
            <table className="w-full text-left border-collapse">
              <thead>
                <tr className="border-b border-slate-100">
                  <th className="py-4 text-xs font-black uppercase text-slate-400 tracking-widest">Operation Sub-Domain</th>
                  {res.domainAverages.map(d => (
                    <th key={d.name} className="py-4 text-center text-xs font-black uppercase text-slate-400 tracking-widest">{d.name} Score</th>
                  ))}
                </tr>
              </thead>
              <tbody className="divide-y divide-slate-50">
                {res.radarData.map((row, i) => (
                  <tr key={i} className="hover:bg-slate-50/50 transition-colors">
                    <td className="py-5 font-bold text-slate-700 text-sm">{row.subject}</td>
                    {res.domainAverages.map(d => {
                      const val = row[d.name] || 0;
                      const colorClass = val >= 4 ? 'bg-emerald-100 text-emerald-700' : val >= 2.5 ? 'bg-amber-100 text-amber-700' : 'bg-rose-100 text-rose-700';
                      return (
                        <td key={d.name} className="py-5">
                          <div className="flex justify-center">
                            <div className={`w-12 h-10 rounded-xl flex items-center justify-center font-black text-xs ${colorClass}`}>
                              {val.toFixed(1)}
                            </div>
                          </div>
                        </td>
                      );
                    })}
                  </tr>
                ))}
              </tbody>
            </table>
          </div>
        </div>

        <div className="bg-slate-900 p-12 rounded-[3.5rem] text-white relative overflow-hidden shadow-2xl">
          <div className="absolute top-0 right-0 p-12 opacity-5 pointer-events-none"><BrainCircuit className="w-64 h-64" /></div>
          <div className="relative z-10">
            <div className="flex items-center justify-between mb-10 border-b border-white/10 pb-6">
              <h2 className="text-4xl font-black flex items-center gap-4"><Send className="text-indigo-400" /> Qualitative Strategic Analysis</h2>
              {!aiAnalysis && <Loader2 className="animate-spin text-indigo-400 w-8 h-8" />}
            </div>
            <div className="max-w-4xl mx-auto">
              {aiAnalysis ? (
                <MarkdownRenderer content={aiAnalysis} />
              ) : (
                <div className="flex flex-col items-center gap-6 py-12 text-slate-500 italic">
                   <div className="p-4 bg-slate-800 rounded-full animate-bounce"><AlertCircle className="w-8 h-8 text-amber-400" /></div>
                   <p className="text-center max-w-sm">Analysis is being synthesized by Gemini... If this persists, please try re-submitting.</p>
                   <button onClick={async () => {
                     const freshAnalysis = await generateAIAnalysis(res);
                     await saveState({ aiAnalysis: freshAnalysis });
                   }} className="px-6 py-2 bg-slate-800 text-indigo-400 rounded-xl font-bold flex items-center gap-2 hover:bg-slate-700">
                     <RefreshCw className="w-4 h-4" /> Force Retry Analysis
                   </button>
                </div>
              )}
            </div>
          </div>
        </div>
      </div>
    );
  }

  return null;
}

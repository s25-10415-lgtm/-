# -
import React, { useState, useEffect } from 'react';
import { 
  Heart, 
  Star, 
  Sparkles, 
  Pencil, 
  Calendar, 
  Trophy, 
  Book, 
  GraduationCap, 
  Smile, 
  Eye, 
  Trash2, 
  Download,
  Flower2,
  Crown,
  Search,
  Wand2,
  Ghost,
  Compass,
  Library,
  Send,
  ScrollText,
  Save,
  CheckCircle2
} from 'lucide-react';

const apiKey = "";

const App = () => {
  const [activeTab, setActiveTab] = useState('edit');
  const [activeSection, setActiveSection] = useState('profile');
  const [isSearching, setIsSearching] = useState(false);
  const [aiAnalysis, setAiAnalysis] = useState(null);
  
  // 비밀 서재 관련 상태
  const [searchQuery, setSearchQuery] = useState('');
  const [searchResult, setSearchResult] = useState('');
  const [isAiConsulting, setIsAiConsulting] = useState(false);
  const [personalNote, setPersonalNote] = useState('');
  
  // 전체 기록 상태 (모든 필드 타이핑 가능하도록 초기화)
  const [record, setRecord] = useState({
    profile: { name: '', birth: '', school: '', grade: '1', classNum: '', studentNum: '' },
    attendance: { absent: '0', late: '0', early: '0', leave: '0', note: '' },
    awards: [{ id: 1, name: '', date: '', rank: '', agency: '' }],
    creative: { self: '', club: '', career: '' },
    subjects: [{ id: Date.now(), name: '', score: '', detail: '' }],
    behavior: ''
  });

  const handleInputChange = (section, field, value) => {
    setRecord(prev => ({ ...prev, [section]: { ...prev[section], [field]: value } }));
  };

  const addAward = () => setRecord(prev => ({ ...prev, awards: [...prev.awards, { id: Date.now(), name: '', date: '', rank: '', agency: '' }] }));
  const updateAward = (id, field, value) => setRecord(prev => ({ ...prev, awards: prev.awards.map(a => a.id === id ? { ...a, [field]: value } : a) }));
  const removeAward = (id) => setRecord(prev => ({ ...prev, awards: prev.awards.filter(a => a.id !== id) }));

  const addSubject = () => setRecord(prev => ({ ...prev, subjects: [...prev.subjects, { id: Date.now(), name: '', score: '', detail: '' }] }));
  const updateSubject = (id, field, value) => setRecord(prev => ({ ...prev, subjects: prev.subjects.map(s => s.id === id ? { ...s, [field]: value } : s) }));
  const removeSubject = (id) => setRecord(prev => ({ ...prev, subjects: prev.subjects.filter(s => s.id !== id) }));

  // 마법의 거울: 대학 추천 AI 서비스 (디자인 개선)
  const searchColleges = async () => {
    if (!record.profile.name) {
      alert("공주님의 이름을 먼저 프로필에 입력해 주세요! ✨");
      return;
    }
    setIsSearching(true);
    setAiAnalysis(null);
    
    const userPrompt = `
      학생의 생활기록부 데이터를 기반으로 지원 가능한 한국 대학교 3곳을 추천해줘.
      데이터: ${JSON.stringify(record)}
      
      응답은 반드시 아래의 JSON 형식으로만 해줘:
      {
        "recommendations": [
          { "univ": "대학명", "dept": "추천학과", "reason": "추천 이유 한 문장" }
        ],
        "tip": "공주님을 위한 따뜻한 조언 한마디"
      }
    `;
    
    try {
      const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ contents: [{ parts: [{ text: userPrompt }] }], generationConfig: { responseMimeType: "application/json" } })
      });
      const data = await response.json();
      const parsedContent = JSON.parse(data.candidates[0].content.parts[0].text);
      setAiAnalysis(parsedContent);
    } catch (e) {
      setAiAnalysis({ error: "거울이 잠시 흐려졌습니다. 잠시 후 다시 시도해 주세요." });
    } finally {
      setIsSearching(false);
    }
  };

  // 비밀 서재: 자유 검색 기능
  const handleGeneralSearch = async () => {
    if (!searchQuery.trim()) return;
    setIsAiConsulting(true);
    
    try {
      const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ 
          contents: [{ parts: [{ text: searchQuery }] }],
          systemInstruction: { parts: [{ text: "당신은 우아하고 친절한 공주님의 집사입니다. 공주님의 질문에 예의 바르고 정보가 풍부하게 대답하세요." }] },
          tools: [{ "google_search": {} }]
        })
      });
      const data = await response.json();
      setSearchResult(data.candidates[0].content.parts[0].text);
    } catch (e) {
      setSearchResult("죄송합니다, 공주님. 정보를 찾는 중에 작은 문제가 발생했습니다.");
    } finally {
      setIsAiConsulting(false);
    }
  };

  const SectionButton = ({ id, icon: Icon, label, color }) => (
    <button
      onClick={() => setActiveSection(id)}
      className={`flex items-center gap-3 px-5 py-4 rounded-full transition-all border-2 ${
        activeSection === id ? `${color} text-white shadow-lg border-white transform scale-105` : 'bg-white/80 text-pink-400 border-pink-100 hover:bg-pink-50'
      }`}
    >
      <Icon size={18} /> <span className="font-bold">{label}</span>
    </button>
  );

  return (
    <div className="min-h-screen bg-[#FFF5F8] text-gray-700 font-sans p-4 md:p-8">
      <div className="max-w-5xl mx-auto relative">
        <header className="flex flex-col items-center mb-10 text-center">
          <h1 className="text-4xl md:text-5xl font-serif font-black text-pink-600 mb-2 flex items-center gap-3">
             프린세스 다이어리 <Crown className="text-yellow-400 fill-yellow-400 animate-bounce" size={40} />
          </h1>
          <p className="text-pink-400 font-medium italic">"공주님의 지혜와 기록이 마법처럼 빛나는 곳"</p>
          
          <div className="flex flex-wrap justify-center bg-white/60 backdrop-blur-md rounded-3xl shadow-lg p-2 mt-8 border-2 border-white gap-2">
            {[
              { id: 'edit', label: '기록하기', icon: Pencil, color: 'from-pink-400 to-rose-400' },
              { id: 'preview', label: '모아보기', icon: Eye, color: 'from-purple-400 to-indigo-400' },
              { id: 'college', label: '마법의 거울', icon: Compass, color: 'from-yellow-400 to-orange-400' },
              { id: 'library', label: '비밀 서재', icon: Library, color: 'from-teal-400 to-emerald-400' }
            ].map(tab => (
              <button 
                key={tab.id}
                onClick={() => setActiveTab(tab.id)}
                className={`flex items-center gap-2 px-6 py-3 rounded-full transition-all font-bold ${activeTab === tab.id ? `bg-gradient-to-r ${tab.color} text-white shadow-md` : 'text-gray-400 hover:text-pink-400'}`}
              >
                <tab.icon size={18} /> {tab.label}
              </button>
            ))}
          </div>
        </header>

        {activeTab === 'edit' && (
          <div className="grid grid-cols-1 md:grid-cols-4 gap-6 animate-fadeIn">
            <div className="md:col-span-1 flex flex-col gap-3">
              <SectionButton id="profile" icon={Smile} label="프로필" color="bg-pink-400" />
              <SectionButton id="attendance" icon={Calendar} label="출결기록" color="bg-rose-300" />
              <SectionButton id="awards" icon={Trophy} label="수상경력" color="bg-yellow-400" />
              <SectionButton id="creative" icon={Star} label="창체활동" color="bg-purple-400" />
              <SectionButton id="subjects" icon={Book} label="세특기록" color="bg-indigo-300" />
              <SectionButton id="behavior" icon={GraduationCap} label="행동발달" color="bg-pink-500" />
            </div>
            <div className="md:col-span-3 bg-white/90 rounded-[2.5rem] shadow-2xl border-8 border-pink-50 p-8 min-h-[550px] transition-all">
                {activeSection === 'profile' && (
                  <div className="space-y-6 animate-fadeIn">
                    <h2 className="text-2xl font-serif font-black text-pink-500 flex items-center gap-2"><Smile /> 공주님 프로필</h2>
                    <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
                      <div className="space-y-2">
                        <label className="text-xs font-bold text-pink-300 ml-2">공주님 함자</label>
                        <input placeholder="이름을 입력하세요" className="w-full p-4 bg-pink-50/50 rounded-2xl outline-none border-2 border-transparent focus:border-pink-200 font-bold" onChange={e => handleInputChange('profile', 'name', e.target.value)} value={record.profile.name} />
                      </div>
                      <div className="space-y-2">
                        <label className="text-xs font-bold text-pink-300 ml-2">탄생일</label>
                        <input type="date" className="w-full p-4 bg-pink-50/50 rounded-2xl outline-none font-bold text-pink-500" onChange={e => handleInputChange('profile', 'birth', e.target.value)} value={record.profile.birth} />
                      </div>
                      <div className="md:col-span-2 space-y-2">
                        <label className="text-xs font-bold text-pink-300 ml-2">나의 왕국 (학교명)</label>
                        <input placeholder="다니시는 학교를 입력하세요" className="w-full p-4 bg-pink-50/50 rounded-2xl font-bold" onChange={e => handleInputChange('profile', 'school', e.target.value)} value={record.profile.school} />
                      </div>
                      <div className="grid grid-cols-3 gap-4 md:col-span-2">
                        <input placeholder="학년" type="number" className="p-4 bg-pink-50/50 rounded-2xl font-bold text-center" onChange={e => handleInputChange('profile', 'grade', e.target.value)} value={record.profile.grade} />
                        <input placeholder="반" type="number" className="p-4 bg-pink-50/50 rounded-2xl font-bold text-center" onChange={e => handleInputChange('profile', 'classNum', e.target.value)} value={record.profile.classNum} />
                        <input placeholder="번호" type="number" className="p-4 bg-pink-50/50 rounded-2xl font-bold text-center" onChange={e => handleInputChange('profile', 'studentNum', e.target.value)} value={record.profile.studentNum} />
                      </div>
                    </div>
                  </div>
                )}

                {activeSection === 'attendance' && (
                  <div className="space-y-6 animate-fadeIn">
                    <h2 className="text-2xl font-serif font-black text-rose-400">🎀 성실함 기록</h2>
                    <div className="grid grid-cols-2 md:grid-cols-4 gap-4">
                      {['absent', 'late', 'early', 'leave'].map((type) => (
                        <div key={type} className="bg-rose-50/50 p-4 rounded-3xl text-center border-2 border-white shadow-sm">
                          <label className="block text-[10px] font-black text-rose-300 mb-2 uppercase">{type}</label>
                          <input type="number" value={record.attendance[type]} onChange={(e) => handleInputChange('attendance', type, e.target.value)} className="w-full bg-white p-2 rounded-xl text-center font-bold text-rose-500 outline-none" />
                        </div>
                      ))}
                    </div>
                    <textarea placeholder="특이사항을 자유롭게 적어주세요..." className="w-full p-6 bg-rose-50/30 rounded-3xl h-40 outline-none border-2 border-dashed border-rose-100 font-medium" value={record.attendance.note} onChange={e => handleInputChange('attendance', 'note', e.target.value)} />
                  </div>
                )}

                {activeSection === 'awards' && (
                  <div className="space-y-4 animate-fadeIn">
                    <div className="flex justify-between items-center mb-4">
                      <h2 className="text-2xl font-serif font-black text-yellow-500">🏆 수상 경력</h2>
                      <button onClick={addAward} className="bg-yellow-400 text-white px-4 py-2 rounded-full text-xs font-bold">+ 추가</button>
                    </div>
                    {record.awards.map(award => (
                      <div key={award.id} className="grid grid-cols-1 md:grid-cols-4 gap-3 p-4 bg-yellow-50/50 rounded-2xl border-2 border-white relative">
                        <input placeholder="상장 이름" className="p-3 bg-white rounded-xl text-sm font-bold" value={award.name} onChange={e => updateAward(award.id, 'name', e.target.value)} />
                        <input placeholder="등급/위" className="p-3 bg-white rounded-xl text-sm font-bold" value={award.rank} onChange={e => updateAward(award.id, 'rank', e.target.value)} />
                        <input placeholder="수여 기관" className="p-3 bg-white rounded-xl text-sm font-bold" value={award.agency} onChange={e => updateAward(award.id, 'agency', e.target.value)} />
                        <div className="flex gap-2">
                          <input type="date" className="flex-1 p-3 bg-white rounded-xl text-xs font-bold" value={award.date} onChange={e => updateAward(award.id, 'date', e.target.value)} />
                          <button onClick={() => removeAward(award.id)} className="text-rose-400 hover:scale-110 transition-transform"><Trash2 size={20} /></button>
                        </div>
                      </div>
                    ))}
                  </div>
                )}

                {activeSection === 'creative' && (
                  <div className="space-y-6 animate-fadeIn">
                    <h2 className="text-2xl font-serif font-black text-purple-400">✨ 창의적 체험활동</h2>
                    {Object.keys(record.creative).map(key => (
                      <div key={key} className="space-y-2">
                        <label className="text-xs font-black text-purple-300 uppercase ml-2">{key === 'self' ? '자율활동' : key === 'club' ? '동아리활동' : '진로활동'}</label>
                        <textarea 
                          placeholder={`${key} 활동 내용을 입력하세요...`} 
                          className="w-full p-5 bg-purple-50/30 border-2 border-white rounded-3xl h-28 outline-none focus:border-purple-200 transition-all font-medium"
                          value={record.creative[key]}
                          onChange={e => handleInputChange('creative', key, e.target.value)}
                        />
                      </div>
                    ))}
                  </div>
                )}

                {activeSection === 'subjects' && (
                  <div className="space-y-6 animate-fadeIn">
                    <div className="flex justify-between items-center">
                      <h2 className="text-2xl font-serif font-black text-indigo-400">📖 교과 세부능력 및 특기사항</h2>
                      <button onClick={addSubject} className="bg-indigo-400 text-white px-4 py-2 rounded-full text-xs font-bold">+ 과목 추가</button>
                    </div>
                    {record.subjects.map(s => (
                      <div key={s.id} className="p-5 bg-indigo-50/30 rounded-[2rem] border-2 border-white space-y-3 relative">
                        <div className="flex gap-3">
                          <input placeholder="과목명" className="flex-1 p-3 bg-white rounded-xl font-bold text-indigo-500" value={s.name} onChange={e => updateSubject(s.id, 'name', e.target.value)} />
                          <input placeholder="성취도" className="w-24 p-3 bg-white rounded-xl font-bold text-center" value={s.score} onChange={e => updateSubject(s.id, 'score', e.target.value)} />
                          <button onClick={() => removeSubject(s.id)} className="text-indigo-300"><Trash2 size={20} /></button>
                        </div>
                        <textarea placeholder="선생님의 기록을 여기에 옮겨보세요..." className="w-full p-4 bg-white/70 rounded-2xl h-24 text-sm font-medium outline-none" value={s.detail} onChange={e => updateSubject(s.id, 'detail', e.target.value)} />
                      </div>
                    ))}
                  </div>
                )}

                {activeSection === 'behavior' && (
                  <div className="space-y-6 animate-fadeIn">
                    <h2 className="text-2xl font-serif font-black text-pink-500">🌸 행동특성 및 종합의견</h2>
                    <textarea 
                      placeholder="올 한 해 공주님의 성장을 아름답게 기록해 주세요..." 
                      className="w-full p-8 bg-pink-50/30 border-4 border-white rounded-[3rem] h-80 outline-none shadow-inner font-medium text-pink-700 italic leading-relaxed"
                      value={record.behavior}
                      onChange={e => handleInputChange('behavior', 'behavior', e.target.value)}
                    />
                  </div>
                )}
            </div>
          </div>
        )}

        {activeTab === 'library' && (
          <div className="grid grid-cols-1 md:grid-cols-2 gap-8 animate-fadeIn">
            <div className="bg-white/90 rounded-[3rem] p-8 shadow-xl border-8 border-teal-50">
              <h2 className="text-2xl font-serif font-black text-teal-600 mb-6 flex items-center gap-2"><Search /> 지식의 검색</h2>
              <div className="relative mb-6">
                <input 
                  type="text" 
                  value={searchQuery}
                  onChange={(e) => setSearchQuery(e.target.value)}
                  onKeyPress={(e) => e.key === 'Enter' && handleGeneralSearch()}
                  placeholder="무엇이 궁금하신가요, 공주님?" 
                  className="w-full p-5 pr-14 bg-teal-50 rounded-3xl border-2 border-transparent focus:border-teal-200 outline-none font-medium"
                />
                <button onClick={handleGeneralSearch} className="absolute right-3 top-3 p-2 bg-teal-400 text-white rounded-2xl"><Send size={24} /></button>
              </div>
              <div className="bg-white rounded-3xl p-6 min-h-[300px] border-2 border-teal-50 overflow-y-auto max-h-[400px]">
                {isAiConsulting ? <div className="flex flex-col items-center justify-center h-full text-teal-300 animate-pulse"><Sparkles size={40} /><p className="mt-2 font-bold">서재를 뒤적이는 중...</p></div> 
                : searchResult ? <div className="text-gray-700 leading-relaxed font-medium whitespace-pre-wrap">{searchResult}</div> 
                : <div className="flex flex-col items-center justify-center h-full text-gray-300 opacity-30"><ScrollText size={50} /></div>}
              </div>
            </div>
            <div className="bg-white/90 rounded-[3rem] p-8 shadow-xl border-8 border-emerald-50">
              <div className="flex justify-between items-center mb-6">
                <h2 className="text-2xl font-serif font-black text-emerald-600 flex items-center gap-2"><Pencil /> 비밀 일기</h2>
                <Save className="text-emerald-300" />
              </div>
              <textarea 
                value={personalNote}
                onChange={(e) => setPersonalNote(e.target.value)}
                placeholder="자유로운 생각들을 적어보세요..."
                className="w-full h-[450px] p-8 bg-emerald-50/20 rounded-[2.5rem] border-2 border-dashed border-emerald-100 outline-none font-medium leading-relaxed resize-none"
                style={{ backgroundImage: 'linear-gradient(transparent, transparent 27px, #e2e8f0 27px)', backgroundSize: '100% 28px' }}
              />
            </div>
          </div>
        )}

        {activeTab === 'college' && (
          <div className="max-w-4xl mx-auto animate-fadeIn">
            <div className="bg-white/95 rounded-[3.5rem] border-8 border-yellow-100 p-10 shadow-2xl relative overflow-hidden text-center">
               <div className="absolute top-0 right-0 w-32 h-32 bg-yellow-50 rounded-bl-full -mr-10 -mt-10 opacity-50"></div>
               
               <h2 className="text-4xl font-serif font-black text-yellow-600 mb-4 flex items-center justify-center gap-4">
                 <Wand2 className="animate-pulse" /> 마법의 거울 <Sparkles className="text-orange-400" />
               </h2>
               <p className="text-orange-400 font-bold mb-10">"공주님의 소중한 기록을 읽고 미래를 비춰보겠습니다"</p>

               <button 
                 onClick={searchColleges}
                 disabled={isSearching}
                 className={`px-16 py-6 rounded-full font-black text-2xl transition-all shadow-2xl mb-12 flex items-center gap-4 mx-auto ${isSearching ? 'bg-gray-200 text-gray-400 cursor-not-allowed' : 'bg-gradient-to-r from-yellow-400 via-orange-400 to-rose-400 text-white hover:scale-105 active:scale-95'}`}
               >
                 {isSearching ? "기록을 분석하고 있습니다..." : "운명 확인하기 ✨"}
               </button>

               {aiAnalysis && !aiAnalysis.error && (
                 <div className="grid grid-cols-1 md:grid-cols-3 gap-6 text-left animate-fadeIn">
                    {aiAnalysis.recommendations.map((rec, i) => (
                      <div key={i} className="bg-white p-6 rounded-[2.5rem] border-2 border-yellow-100 shadow-lg transform hover:-translate-y-2 transition-all">
                         <div className="bg-yellow-400 text-white w-10 h-10 rounded-full flex items-center justify-center font-black mb-4 shadow-md">{i+1}</div>
                         <h3 className="font-black text-xl text-gray-800 mb-1">{rec.univ}</h3>
                         <p className="text-orange-500 font-bold mb-3">{rec.dept}</p>
                         <p className="text-xs text-gray-500 font-medium leading-relaxed">{rec.reason}</p>
                      </div>
                    ))}
                    <div className="md:col-span-3 mt-8 bg-gradient-to-r from-pink-500 to-rose-500 p-8 rounded-[3rem] text-white shadow-xl relative overflow-hidden">
                       <Flower2 className="absolute right-[-20px] bottom-[-20px] opacity-20" size={150} />
                       <h4 className="font-black text-xl mb-3 flex items-center gap-2"><CheckCircle2 size={24} /> 공주님을 위한 한마디</h4>
                       <p className="text-lg font-medium italic opacity-95">"{aiAnalysis.tip}"</p>
                    </div>
                 </div>
               )}

               {aiAnalysis?.error && (
                 <div className="p-10 bg-rose-50 text-rose-500 rounded-3xl font-bold border-2 border-white animate-bounce">
                   {aiAnalysis.error}
                 </div>
               )}

               {!aiAnalysis && !isSearching && (
                 <div className="py-20 text-yellow-200 opacity-40">
                   <Ghost size={100} className="mx-auto" />
                   <p className="mt-4 font-black">아직 비추어 본 운명이 없습니다.</p>
                 </div>
               )}
            </div>
          </div>
        )}
        
        {activeTab === 'preview' && (
          <div className="bg-white p-12 rounded-[3.5rem] shadow-2xl border-[12px] border-pink-50 min-h-[800px] animate-fadeIn">
            <div className="max-w-2xl mx-auto">
              <div className="text-center mb-16 border-b-2 border-pink-100 pb-10">
                <Crown className="mx-auto text-yellow-400 mb-4" size={50} />
                <h2 className="text-4xl font-serif font-black text-pink-600">Princess Journal</h2>
                <p className="text-pink-300 font-bold mt-2 uppercase tracking-widest">{record.profile.school || "설레는 왕국 고등학교"}</p>
              </div>
              
              <div className="space-y-12">
                <section className="flex items-center gap-8 bg-pink-50/30 p-8 rounded-[2.5rem] border-2 border-white">
                  <div className="w-24 h-24 bg-white rounded-full flex items-center justify-center shadow-inner text-pink-200"><Smile size={50} /></div>
                  <div>
                    <h4 className="text-[10px] font-black text-pink-300 uppercase tracking-tighter">Student Name</h4>
                    <p className="text-2xl font-black text-pink-600">{record.profile.name || "미지의 공주님"}</p>
                    <p className="text-sm font-bold text-pink-400">{record.profile.grade}학년 {record.profile.classNum}반 {record.profile.studentNum}번</p>
                  </div>
                </section>

                <section className="space-y-6">
                  <h3 className="font-black text-pink-500 border-l-4 border-pink-400 pl-4">학습 및 활동 기록</h3>
                  <div className="grid gap-4">
                    {record.subjects.map(s => s.name && (
                      <div key={s.id} className="bg-white p-5 rounded-3xl border border-pink-100">
                        <div className="flex justify-between mb-2">
                          <span className="font-black text-gray-700">{s.name}</span>
                          <span className="bg-pink-100 text-pink-500 px-3 py-1 rounded-full text-xs font-bold">{s.score}</span>
                        </div>
                        <p className="text-sm text-gray-500 leading-relaxed italic">{s.detail}</p>
                      </div>
                    ))}
                  </div>
                </section>
              </div>
              <div className="mt-20 text-center opacity-30 italic text-sm">소중한 꿈은 반드시 이루어질 거예요.</div>
            </div>
          </div>
        )}
      </div>

      <style>{`
        .animate-fadeIn { animation: fadeIn 0.6s ease-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }
        input::-webkit-outer-spin-button, input::-webkit-inner-spin-button { -webkit-appearance: none; margin: 0; }
      `}</style>
    </div>
  );
};

export default App;

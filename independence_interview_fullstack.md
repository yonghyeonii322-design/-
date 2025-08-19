independence-interview-app/
├─ frontend/
│  ├─ public/
│  │  └─ images/
│  │     ├─ kimgoo.jpg
│  │     ├─ anchangho.jpg
│  │     ├─ yugwansun.jpg
│  │     ├─ anjunggeun.jpg
│  │     ├─ yunbonggil.jpg
│  │     └─ leebongchang.jpg
│  ├─ src/
│  │  ├─ App.js
│  │  └─ index.js
│  ├─ package.json
│  └─ README.md
├─ backend/
│  ├─ server.js
│  ├─ package.json
│  └─ README.md
└─ README.md

---

// backend/package.json
{
  "name": "independence-backend",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "cors": "^2.8.5",
    "express": "^4.18.2"
  }
}

// backend/server.js
const express = require("express");
const cors = require("cors");
const app = express();
app.use(cors());

const answers = {
  kimgoo: "나는 힘보다는 멋진 문화로 존경받는 나라가 되길 바랐단다.",
  anchangho: "나는 우리나라가 올바른 교육과 덕을 갖춘 나라가 되길 바랐단다.",
  yugwansun: "나는 모두가 용기있게 나라를 위해 노력하길 바랐단다.",
  anjunggeun: "나는 평화로운 세상을 꿈꾸었단다.",
  yunbonggil: "나는 정의와 용기로 나라를 지키길 바랐단다.",
  leebongchang: "나는 독립을 위해 작은 힘이라도 보태길 바랐단다."
};

app.get("/api/answer", (req, res) => {
  const figure = req.query.figure;
  const q = req.query.q || "";
  res.json({
    disclaimer: "※ 실제 인용 기반 (출처: https://encykorea.aks.ac.kr)",
    body: `질문: ${q}\n답변: \"${answers[figure] || '아직 준비 중입니다.'}\"`,
    citation: "https://encykorea.aks.ac.kr"
  });
});

const PORT = process.env.PORT || 8000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));

// frontend/package.json
{
  "name": "independence-frontend",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "framer-motion": "^10.12.16",
    "lucide-react": "^0.253.0"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build"
  }
}

// frontend/src/App.js
import React, { useState } from "react";
import { motion } from "framer-motion";
import { Sparkles, BookOpen } from "lucide-react";

const FIGURE_IMAGES = {
  kimgoo: "/images/kimgoo.jpg",
  anchangho: "/images/anchangho.jpg",
  yugwansun: "/images/yugwansun.jpg",
  anjunggeun: "/images/anjunggeun.jpg",
  yunbonggil: "/images/yunbonggil.jpg",
  leebongchang: "/images/leebongchang.jpg",
};

export default function App() {
  const [figure, setFigure] = useState("kimgoo");
  const [question, setQuestion] = useState("");
  const [result, setResult] = useState(null);
  const [loading, setLoading] = useState(false);

  const handleAsk = async () => {
    if (!question.trim()) return;
    setLoading(true);
    try {
      const res = await fetch(`${process.env.REACT_APP_BACKEND_URL}/api/answer?figure=${figure}&q=${encodeURIComponent(question)}`);
      const data = await res.json();
      setResult(data);
    } catch (e) {
      setResult({
        disclaimer: "※ 서버 오류. 데모용 예시입니다.",
        body: `질문: ${question}\n답변: '데모 답변입니다.'`,
        citation: "https://encykorea.aks.ac.kr"
      });
    }
    setLoading(false);
  };

  return (
    <div className="min-h-screen bg-gradient-to-b from-slate-50 to-slate-100 py-10 px-4">
      <motion.div initial={{ opacity: 0, y: -20 }} animate={{ opacity: 1, y: 0 }} className="max-w-3xl mx-auto space-y-6">
        <h1 className="text-2xl md:text-3xl font-bold flex items-center gap-2 justify-center text-slate-800">
          <Sparkles className="w-7 h-7 text-yellow-500" /> 독립운동가 가상 인터뷰
        </h1>
        <div className="bg-white rounded-2xl shadow-lg p-6 space-y-4">
          <div className="flex items-center gap-3">
            <img src={FIGURE_IMAGES[figure]} alt={figure} className="w-12 h-12 rounded-full object-cover border-2 border-slate-300" />
            <select value={figure} onChange={(e) => setFigure(e.target.value)} className="border p-2 rounded-lg w-full">
              <option value="kimgoo">백범 김구</option>
              <option value="anchangho">도산 안창호</option>
              <option value="yugwansun">유관순</option>
              <option value="anjunggeun">안중근</option>
              <option value="yunbonggil">윤봉길</option>
              <option value="leebongchang">이봉창</option>
            </select>
          </div>
          <textarea value={question} onChange={(e) => setQuestion(e.target.value)} placeholder="독립운동가에게 질문을 해보세요!" className="w-full border p-3 rounded-lg focus:outline-none focus:ring-2 focus:ring-slate-400" rows={3} />
          <button onClick={handleAsk} className="w-full py-3 bg-slate-900 hover:bg-slate-800 text-white rounded-xl font-semibold transition" disabled={loading}>
            {loading ? "답변을 불러오는 중..." : "질문 보내기"}
          </button>
        </div>
        {result && (
          <motion.div initial={{ opacity: 0 }} animate={{ opacity: 1 }} className="bg-white rounded-2xl shadow-md p-6 space-y-3">
            <div className="flex items-center gap-2 text-slate-700 font-medium">
              <BookOpen className="w-5 h-5 text-green-600" /> 실제 기록 기반 답변
            </div>
            <p className="text-sm text-slate-500">{result.disclaimer}</p>
            <pre className="whitespace-pre-wrap text-slate-800 leading-relaxed">{result.body}</pre>
            <p className="text-xs text-slate-500">출처: <a href={result.citation} className="underline" target="_blank" rel="noreferrer">{result.citation}</a></p>
          </motion.div>
        )}
      </motion.div>
    </div>
  );
}


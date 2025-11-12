import React, { useState } from "react";
import { motion } from "framer-motion";

// Mô phỏng: Học Hằng Đẳng Thức Đáng Nhớ
// File đơn: default export React component
// Thiết kế: Tailwind CSS classes (sử dụng trong môi trường hỗ trợ Tailwind)

export default function HDDNSimulator() {
  const identities = [
    {
      id: "sq_plus",
      title: "(a + b)^2",
      formula: "(a + b)^2 = a^2 + 2ab + b^2",
      expand: (a, b) => `${a}^2 + 2*${a}*${b} + ${b}^2`,
      hint: "Nhân (a+b) với chính nó, ta có các hạng tử: a^2, 2ab, b^2",
    },
    {
      id: "sq_minus",
      title: "(a - b)^2",
      formula: "(a - b)^2 = a^2 - 2ab + b^2",
      expand: (a, b) => `${a}^2 - 2*${a}*${b} + ${b}^2`,
      hint: "Chú ý dấu trừ: dấu ở trung gian thành -2ab",
    },
    {
      id: "diff_sq",
      title: "a^2 - b^2",
      formula: "a^2 - b^2 = (a - b)(a + b)",
      expand: (a, b) => `(${a} - ${b})(${a} + ${b})`,
      hint: "Hiệu của hai bình phương có thể phân tích thành tích hai nhị thức",
    },
  ];

  const [selected, setSelected] = useState(identities[0]);
  const [a, setA] = useState(3);
  const [b, setB] = useState(2);
  const [mode, setMode] = useState("learn"); // learn | practice | game
  const [userAnswer, setUserAnswer] = useState("");
  const [feedback, setFeedback] = useState(null);
  const [score, setScore] = useState(0);
  const [qCount, setQCount] = useState(0);

  function evaluateAnswer(id, aV, bV, ans) {
    const clean = ans.replace(/\s+/g, "").replace(/\*/g, "");
    let correct;
    if (id === "sq_plus") correct = `${aV}^2+2${aV}${bV}+${bV}^2`;
    if (id === "sq_minus") correct = `${aV}^2-2${aV}${bV}+${bV}^2`;
    if (id === "diff_sq") correct = `(${aV}-${bV})(${aV}+${bV})`;
    const ok = clean.includes(correct.replace(/\s+/g, ""));
    return ok;
  }

  function handleCheck() {
    const ok = evaluateAnswer(selected.id, a, b, userAnswer);
    setFeedback(ok ? "Chính xác 🎉" : "Chưa đúng — xem gợi ý hoặc thử lại");
  }

  function randomQuestion() {
    const id = identities[Math.floor(Math.random() * identities.length)];
    setSelected(id);
    const ra = Math.floor(Math.random() * 9) + 1;
    const rb = Math.floor(Math.random() * 9) + 1;
    setA(ra);
    setB(rb);
    setUserAnswer("");
    setFeedback(null);
  }

  function handleSubmitGame() {
    const ok = evaluateAnswer(selected.id, a, b, userAnswer);
    setQCount(qCount + 1);
    if (ok) setScore(score + 1);
    setFeedback(ok ? "Đúng — +1 điểm" : "Sai — 0 điểm");
    // auto next question
    setTimeout(() => {
      randomQuestion();
    }, 900);
  }

  return (
    <div className="min-h-screen p-6 bg-gradient-to-b from-sky-50 to-white">
      <div className="max-w-4xl mx-auto bg-white rounded-2xl shadow-lg p-6">
        <header className="flex items-center justify-between mb-6">
          <div>
            <h1 className="text-2xl font-extrabold">Mô phỏng: Hằng đẳng thức đáng nhớ</h1>
            <p className="text-sm text-gray-600">Dành cho học sinh lớp 7 — tương tác, luyện tập và chơi nhỏ.</p>
          </div>
          <div className="space-x-2">
            <button
              onClick={() => setMode("learn")}
              className={`px-3 py-1 rounded ${mode === "learn" ? "bg-sky-600 text-white" : "bg-gray-100"}`}
            >
              Học
            </button>
            <button
              onClick={() => setMode("practice")}
              className={`px-3 py-1 rounded ${mode === "practice" ? "bg-sky-600 text-white" : "bg-gray-100"}`}
            >
              Luyện
            </button>
            <button
              onClick={() => { setMode("game"); randomQuestion(); }}
              className={`px-3 py-1 rounded ${mode === "game" ? "bg-sky-600 text-white" : "bg-gray-100"}`}
            >
              Trò chơi
            </button>
          </div>
        </header>

        <main className="grid md:grid-cols-3 gap-6">
          <section className="md:col-span-1">
            <div className="bg-slate-50 p-4 rounded-lg">
              <h3 className="font-semibold mb-2">Chọn hằng đẳng thức</h3>
              <div className="space-y-3">
                {identities.map((it) => (
                  <button
                    key={it.id}
                    onClick={() => setSelected(it)}
                    className={`w-full text-left p-3 rounded-lg border ${selected.id === it.id ? "border-sky-500 bg-sky-50" : "border-gray-200"}`}
                  >
                    <div className="font-medium">{it.title}</div>
                    <div className="text-xs text-gray-500">{it.formula}</div>
                  </button>
                ))}
              </div>

              <div className="mt-4">
                <h4 className="font-medium">Điều chỉnh giá trị</h4>
                <div className="flex items-center gap-3 mt-2">
                  <label className="text-sm">a</label>
                  <input type="range" min="-9" max="9" value={a} onChange={(e) => setA(parseInt(e.target.value))} />
                  <div className="w-10 text-center font-bold">{a}</div>
                </div>
                <div className="flex items-center gap-3 mt-2">
                  <label className="text-sm">b</label>
                  <input type="range" min="-9" max="9" value={b} onChange={(e) => setB(parseInt(e.target.value))} />
                  <div className="w-10 text-center font-bold">{b}</div>
                </div>
                <button onClick={() => { setA(3); setB(2); }} className="mt-3 px-3 py-1 bg-gray-100 rounded">Đặt lại</button>
              </div>
            </div>

            <div className="mt-4 bg-slate-50 p-4 rounded-lg">
              <h4 className="font-semibold">Ví dụ số</h4>
              <p className="text-sm text-gray-600">Thay a và b vào công thức</p>
              <div className="mt-2 p-3 bg-white rounded">
                <div className="text-lg font-medium">{selected.title}</div>
                <div className="text-sm text-gray-700 mt-1">Công thức: {selected.formula}</div>
                <div className="mt-2 text-sm">Thay a = <span className="font-bold">{a}</span>, b = <span className="font-bold">{b}</span></div>
                <div className="mt-2 font-mono bg-gray-50 p-2 rounded">Kết quả: {selected.expand(a, b)}</div>
              </div>
            </div>

          </section>

          <section className="md:col-span-2">
            <div className="bg-white rounded-lg p-4 shadow-sm">
              <h3 className="font-semibold">{mode === "learn" ? "Học và giải thích" : mode === "practice" ? "Bài tập luyện" : "Trò chơi: trả lời nhanh"}</h3>

              {mode === "learn" && (
                <div className="mt-4 space-y-4">
                  <div className="p-4 bg-sky-50 rounded">
                    <div className="font-medium">Giải thích trực quan</div>
                    <p className="text-sm text-gray-700 mt-2">Dùng hình vuông để thấy (a+b)^2 = a^2 + 2ab + b^2: một hình vuông cạnh (a+b) chia thành 4 phần (a^2, 2ab, b^2).</p>
                    <motion.div className="mt-3 p-3 bg-white rounded flex gap-3 justify-center" layout>
                      <div className="flex flex-col items-center">
                        <div className="w-32 h-32 border-2 border-sky-300 relative" style={{fontSize:12}}>
                          <div className="absolute top-0 left-0 w-1/2 h-1/2 border-r border-b border-sky-200 flex items-center justify-center">a^2</div>
                          <div className="absolute top-0 right-0 w-1/2 h-1/2 border-l border-b border-sky-200 flex items-center justify-center">ab</div>
                          <div className="absolute bottom-0 left-0 w-1/2 h-1/2 border-r border-t border-sky-200 flex items-center justify-center">ab</div>
                          <div className="absolute bottom-0 right-0 w-1/2 h-1/2 border-l border-t border-sky-200 flex items-center justify-center">b^2</div>
                        </div>
                        <div className="text-xs text-gray-500 mt-2">Phân tích diện tích</div>
                      </div>
                    </motion.div>
                  </div>

                  <div className="p-4 bg-amber-50 rounded">
                    <div className="font-medium">Gợi ý bước giải</div>
                    <ol className="list-decimal pl-5 text-sm mt-2 text-gray-700">
                      <li>Nhân nhị thức với chính nó (hoặc phân tích hiệu hai bình phương).</li>
                      <li>Nhóm các hạng tử cùng loại (a^2, ab, b^2).</li>
                      <li>Kiểm tra dấu trước khi kết luận (dấu cộng/trừ).</li>
                    </ol>
                  </div>

                  <div className="p-4 bg-emerald-50 rounded">
                    <div className="font-medium">Gợi ý nâng cao</div>
                    <p className="text-sm text-gray-700 mt-2">Thử nhân với số cụ thể để kiểm tra tính đúng: ví dụ a=3, b=2 => (3+2)^2 = 25 và phải bằng 3^2 + 2*3*2 + 2^2 = 9 + 12 + 4 = 25.</p>
                  </div>
                </div>
              )}

              {mode === "practice" && (
                <div className="mt-4">
                  <div className="flex gap-3 items-center">
                    <div className="text-sm">Bài toán: Viết dạng đã khai triển hoặc phân tích</div>
                    <button onClick={randomQuestion} className="ml-auto px-3 py-1 bg-gray-100 rounded">Bài mới</button>
                  </div>

                  <div className="mt-3 bg-gray-50 p-4 rounded">
                    <div className="font-mono">{selected.title} với a = <span className="font-bold">{a}</span>, b = <span className="font-bold">{b}</span></div>
                    <div className="mt-3">
                      <input value={userAnswer} onChange={(e) => setUserAnswer(e.target.value)} placeholder="Viết biểu thức đã khai triển hoặc phân tích" className="w-full p-2 border rounded" />
                      <div className="flex gap-2 mt-2">
                        <button onClick={handleCheck} className="px-3 py-1 bg-sky-600 text-white rounded">Kiểm tra</button>
                        <button onClick={() => setUserAnswer(selected.expand(a, b))} className="px-3 py-1 bg-gray-100 rounded">Gợi ý (tự động)</button>
                      </div>
                      {feedback && <div className="mt-2 text-sm">{feedback}</div>}
                      <div className="mt-2 text-xs text-gray-500">Gợi ý: {selected.hint}</div>
                    </div>
                  </div>
                </div>
              )}

              {mode === "game" && (
                <div className="mt-4">
                  <div className="flex items-center justify-between">
                    <div>Điểm: <span className="font-bold">{score}</span> / <span className="font-bold">{qCount}</span></div>
                    <div className="text-sm text-gray-500">Mục tiêu: Trả lời nhanh & đúng</div>
                  </div>

                  <div className="mt-3 bg-gray-50 p-4 rounded">
                    <div className="font-mono">{selected.title} với a = <span className="font-bold">{a}</span>, b = <span className="font-bold">{b}</span></div>
                    <input value={userAnswer} onChange={(e) => setUserAnswer(e.target.value)} placeholder="Nhập biểu thức" className="w-full p-2 border rounded mt-2" />
                    <div className="flex gap-2 mt-2">
                      <button onClick={handleSubmitGame} className="px-3 py-1 bg-amber-500 text-white rounded">Nộp</button>
                      <button onClick={randomQuestion} className="px-3 py-1 bg-gray-100 rounded">Bỏ qua</button>
                    </div>
                    {feedback && <div className="mt-2 text-sm">{feedback}</div>}
                  </div>
                </div>
              )}

            </div>

            <div className="mt-4 p-4 bg-white rounded shadow-sm">
              <h4 className="font-semibold">Tài nguyên cho giáo viên</h4>
              <ul className="list-disc pl-5 text-sm mt-2 text-gray-700">
                <li>Mỗi chế độ có thể dùng khi ôn tập hoặc làm việc nhóm.</li>
                <li>In danh sách câu hỏi luyện tập để kiểm tra nhanh.</li>
                <li>Gợi ý: dùng chế độ 'Học' để giải thích trực quan trước khi cho học sinh làm 'Luyện' hoặc 'Trò chơi'.</li>
              </ul>
            </div>

          </section>
        </main>

        <footer className="mt-6 text-center text-xs text-gray-500">Mình có thể mở rộng: thêm dạng phương trình, bài toán chữ, lưu kết quả, in ấn, hoặc xuất thành web độc lập.</footer>
      </div>
    </div>
  );
}

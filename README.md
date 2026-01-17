import React, { useState, useEffect } from 'react';
import { Clipboard, Send, Inbox, List, CheckCircle, MessageSquare, Heart, AlertCircle, Trash2, ChevronRight, Settings, Printer, QrCode, X } from 'lucide-react';

const App = () => {
  const [view, setView] = useState('user'); // 'user', 'admin', or 'poster'
  const [submissions, setSubmissions] = useState([]);
  const [form, setForm] = useState({
    type: '改善アイデア',
    content: '',
    isAnonymous: true,
    author: '',
    timestamp: null
  });
  const [statusMessage, setStatusMessage] = useState('');

  // カテゴリ設定
  const categories = [
    { id: '改善アイデア', icon: <Inbox className="w-5 h-5 text-blue-500" />, color: 'bg-blue-50 border-blue-200' },
    { id: '困りごと・悩み', icon: <AlertCircle className="w-5 h-5 text-red-500" />, color: 'bg-red-50 border-red-200' },
    { id: '感謝・ありがとう', icon: <Heart className="w-5 h-5 text-pink-500" />, color: 'bg-pink-50 border-pink-200' },
    { id: 'その他', icon: <MessageSquare className="w-5 h-5 text-gray-500" />, color: 'bg-gray-50 border-gray-200' },
  ];

  // 投稿処理
  const handleSubmit = (e) => {
    e.preventDefault();
    if (!form.content.trim()) return;

    const newSubmission = {
      ...form,
      id: Date.now(),
      timestamp: new Date().toLocaleString('ja-JP'),
      status: '未対応'
    };

    const updatedSubmissions = [newSubmission, ...submissions];
    setSubmissions(updatedSubmissions);
    
    // リセット
    setForm({
      type: '改善アイデア',
      content: '',
      isAnonymous: true,
      author: '',
      timestamp: null
    });

    setStatusMessage('送信が完了しました。ありがとうございます！');
    setTimeout(() => setStatusMessage(''), 3000);
  };

  // 管理者：ステータス更新
  const updateStatus = (id, newStatus) => {
    setSubmissions(submissions.map(s => s.id === id ? { ...s, status: newStatus } : s));
  };

  // 管理者：削除
  const deleteSubmission = (id) => {
    setSubmissions(submissions.filter(s => s.id !== id));
  };

  // 案内ポスター画面
  const PosterOverlay = () => (
    <div className="fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-50 flex items-center justify-center p-4 overflow-y-auto">
      <div className="bg-white w-full max-w-lg rounded-3xl shadow-2xl relative animate-in zoom-in duration-300 my-8">
        <button 
          onClick={() => setView('admin')}
          className="absolute top-4 right-4 p-2 bg-slate-100 rounded-full hover:bg-slate-200 transition-colors"
        >
          <X className="w-5 h-5" />
        </button>

        <div className="p-8 text-center print:p-0">
          <div className="flex justify-center mb-4">
            <div className="bg-blue-600 p-3 rounded-2xl shadow-lg shadow-blue-200">
              <Inbox className="text-white w-8 h-8" />
            </div>
          </div>
          <h2 className="text-3xl font-black text-slate-900 mb-2">なんでもBOX</h2>
          <p className="text-slate-500 font-medium mb-8 text-lg">〜デジタル版が登場しました〜</p>

          <div className="bg-slate-50 border-2 border-dashed border-slate-200 rounded-3xl p-10 mb-8 flex flex-col items-center justify-center relative group">
            <div className="w-48 h-48 bg-white border border-slate-100 rounded-2xl shadow-inner flex items-center justify-center mb-4">
              <QrCode className="w-40 h-40 text-slate-800" />
            </div>
            <p className="text-xs text-slate-400 font-bold uppercase tracking-widest">Scan to post</p>
            <div className="absolute inset-0 flex items-center justify-center opacity-0 group-hover:opacity-100 transition-opacity bg-white/80 rounded-3xl">
              <p className="text-sm font-bold text-blue-600">実際のURLをQRコード化して<br/>ここに配置してください</p>
            </div>
          </div>

          <div className="space-y-4 text-left bg-blue-50/50 p-6 rounded-2xl border border-blue-100">
            <h3 className="font-bold text-blue-800 flex items-center gap-2">
              <CheckCircle className="w-5 h-5 text-blue-600" />
              安心してご利用ください
            </h3>
            <ul className="text-sm text-blue-700/80 space-y-2 font-medium leading-relaxed">
              <li>・投稿は完全匿名で行えます（特定されません）</li>
              <li>・改善のアイデア、悩み、感謝など何でもOK！</li>
              <li>・スマホでスキャンして、1分で送信完了。</li>
            </ul>
          </div>

          <button 
            onClick={() => window.print()}
            className="mt-8 w-full bg-slate-900 text-white py-4 rounded-xl font-bold flex items-center justify-center gap-2 hover:bg-slate-800 transition-all print:hidden"
          >
            <Printer className="w-5 h-5" />
            この案内を印刷する
          </button>
        </div>
      </div>
    </div>
  );

  return (
    <div className="min-h-screen bg-slate-50 font-sans text-slate-800">
      {view === 'poster' && <PosterOverlay />}

      {/* ヘッダー */}
      <header className="bg-white border-b border-slate-200 sticky top-0 z-10 print:hidden">
        <div className="max-w-2xl mx-auto px-4 h-16 flex items-center justify-between">
          <div className="flex items-center gap-2">
            <div className="bg-blue-600 p-2 rounded-lg">
              <Inbox className="text-white w-5 h-5" />
            </div>
            <h1 className="font-bold text-xl tracking-tight text-slate-900">なんでもBOX</h1>
          </div>
          <div className="flex gap-4">
            {view === 'admin' && (
              <button 
                onClick={() => setView('poster')}
                className="text-sm font-medium text-blue-600 hover:bg-blue-50 px-3 py-1.5 rounded-lg flex items-center gap-1 transition-all"
              >
                <QrCode className="w-4 h-4" />
                案内を作成
              </button>
            )}
            <button 
              onClick={() => setView(view === 'user' ? 'admin' : 'user')}
              className="text-sm font-medium text-slate-500 hover:text-blue-600 flex items-center gap-1 transition-colors"
            >
              {view === 'user' ? <Settings className="w-4 h-4" /> : <List className="w-4 h-4" />}
              {view === 'user' ? '管理者画面' : '投稿画面へ'}
            </button>
          </div>
        </div>
      </header>

      <main className="max-w-2xl mx-auto px-4 py-8 print:hidden">
        {view === 'user' ? (
          /* ユーザー投稿画面 */
          <div className="space-y-6 animate-in fade-in duration-500">
            <div className="bg-gradient-to-br from-blue-600 to-indigo-700 rounded-2xl p-6 text-white shadow-lg shadow-blue-200/50">
              <h2 className="text-2xl font-bold mb-2">お気持ち、お聞かせください</h2>
              <p className="text-blue-100 text-sm leading-relaxed">
                どんな小さなことでもOKです。お寄せいただいたご意見は、担当部署が責任をもって真摯に受け止め、前向きな改善へとつなげてまいります。
              </p>
            </div>

            {statusMessage && (
              <div className="bg-green-100 border border-green-200 text-green-700 px-4 py-3 rounded-xl flex items-center gap-2 animate-bounce">
                <CheckCircle className="w-5 h-5" />
                {statusMessage}
              </div>
            )}

            <form onSubmit={handleSubmit} className="bg-white rounded-2xl shadow-sm border border-slate-200 p-6 space-y-6">
              <div>
                <label className="block text-sm font-bold text-slate-700 mb-3">カテゴリを選んでください</label>
                <div className="grid grid-cols-2 gap-3">
                  {categories.map((cat) => (
                    <button
                      key={cat.id}
                      type="button"
                      onClick={() => setForm({ ...form, type: cat.id })}
                      className={`flex items-center gap-3 p-3 rounded-xl border-2 transition-all ${
                        form.type === cat.id 
                          ? 'border-blue-500 bg-blue-50 ring-2 ring-blue-100' 
                          : 'border-slate-100 bg-white hover:border-slate-200'
                      }`}
                    >
                      {cat.icon}
                      <span className="text-sm font-medium">{cat.id}</span>
                    </button>
                  ))}
                </div>
              </div>

              <div>
                <label className="block text-sm font-bold text-slate-700 mb-2">内容をどうぞ</label>
                <textarea
                  className="w-full h-40 p-4 rounded-xl border border-slate-200 bg-slate-50 focus:bg-white focus:ring-4 focus:ring-blue-100 focus:border-blue-500 transition-all outline-none resize-none"
                  placeholder="「もっとこうなればいいのに」「ここが困っている」「〇〇さんありがとう」など自由にお書きください"
                  value={form.content}
                  onChange={(e) => setForm({ ...form, content: e.target.value })}
                />
              </div>

              <div className="flex items-center justify-between pt-2">
                <div className="flex items-center gap-2">
                  <input
                    type="checkbox"
                    id="anonymous"
                    checked={form.isAnonymous}
                    onChange={(e) => setForm({ ...form, isAnonymous: e.target.checked })}
                    className="w-4 h-4 text-blue-600 rounded focus:ring-blue-500"
                  />
                  <label htmlFor="anonymous" className="text-sm text-slate-600 select-none">匿名で投稿する</label>
                </div>
                {!form.isAnonymous && (
                  <input
                    type="text"
                    placeholder="お名前"
                    className="text-sm border-b border-slate-300 focus:border-blue-500 outline-none pb-1"
                    value={form.author}
                    onChange={(e) => setForm({ ...form, author: e.target.value })}
                  />
                )}
              </div>

              <button
                type="submit"
                disabled={!form.content.trim()}
                className="w-full bg-blue-600 hover:bg-blue-700 disabled:bg-slate-300 text-white font-bold py-4 rounded-xl shadow-lg shadow-blue-200 transition-all flex items-center justify-center gap-2 active:scale-[0.98]"
              >
                <Send className="w-5 h-5" />
                投稿を送信する
              </button>
            </form>
          </div>
        ) : (
          /* 管理者画面 */
          <div className="space-y-6 animate-in slide-in-from-right duration-500">
            <div className="flex items-center justify-between mb-2">
              <h2 className="text-2xl font-bold flex items-center gap-2">
                届いたご意見一覧
                <span className="bg-blue-100 text-blue-700 text-xs px-2 py-1 rounded-full">{submissions.length}件</span>
              </h2>
            </div>

            {submissions.length === 0 ? (
              <div className="bg-white rounded-2xl border-2 border-dashed border-slate-200 p-12 text-center text-slate-400">
                <Inbox className="w-12 h-12 mx-auto mb-4 opacity-20" />
                <p>まだ投稿はありません。</p>
              </div>
            ) : (
              <div className="space-y-4">
                {submissions.map((item) => (
                  <div key={item.id} className="bg-white rounded-2xl border border-slate-200 overflow-hidden shadow-sm hover:shadow-md transition-shadow">
                    <div className="p-5">
                      <div className="flex items-center justify-between mb-4">
                        <div className="flex items-center gap-2">
                          {categories.find(c => c.id === item.type)?.icon}
                          <span className={`text-xs font-bold px-2 py-1 rounded-md ${categories.find(c => c.id === item.type)?.color.replace('border-', 'text-').replace('bg-', 'bg-opacity-50 ')}`}>
                            {item.type}
                          </span>
                          <span className="text-xs text-slate-400">{item.timestamp}</span>
                        </div>
                        <div className="flex gap-1">
                          {['未対応', '検討中', '完了'].map(status => (
                            <button
                              key={status}
                              onClick={() => updateStatus(item.id, status)}
                              className={`text-[10px] px-2 py-1 rounded-full border transition-all ${
                                item.status === status 
                                  ? 'bg-blue-600 border-blue-600 text-white' 
                                  : 'bg-white border-slate-200 text-slate-500 hover:bg-slate-50'
                              }`}
                            >
                              {status}
                            </button>
                          ))}
                          <button 
                            onClick={() => deleteSubmission(item.id)}
                            className="p-1 text-slate-300 hover:text-red-500 transition-colors ml-2"
                          >
                            <Trash2 className="w-4 h-4" />
                          </button>
                        </div>
                      </div>
                      <p className="text-slate-700 whitespace-pre-wrap leading-relaxed mb-4">
                        {item.content}
                      </p>
                      <div className="flex items-center justify-between pt-4 border-t border-slate-50">
                        <span className="text-xs text-slate-500 flex items-center gap-1">
                          投稿者: {item.isAnonymous ? '匿名希望' : (item.author || '不明')}
                        </span>
                        <div className="flex items-center gap-1 text-xs font-bold text-blue-600">
                          ステータス: {item.status}
                          <ChevronRight className="w-3 h-3" />
                        </div>
                      </div>
                    </div>
                  </div>
                ))}
              </div>
            )}
          </div>
        )}
      </main>

      {/* フッター */}
      <footer className="py-12 px-4 text-center text-slate-400 text-xs print:hidden">
        <p>© 2024 なんでもBOX 職場環境改善プロジェクト</p>
      </footer>
    </div>
  );
};

export default App;

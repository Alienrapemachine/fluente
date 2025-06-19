# fluente
app learning 
// === FLUENTE LANGUAGE LEARNING APP ===
// Complete React Application Ready for GitHub

import React, { useState, useEffect, useRef } from 'react';
import './App.css';

// === MAIN APP COMPONENT ===
function App() {
  const [currentView, setCurrentView] = useState('welcome');
  const [userState, setUserState] = useState({
    userId: '',
    username: '',
    email: '',
    profilePicture: '',
    totalXP: 0,
    currentStreak: 0,
    currentLesson: 1,
    unlockedLessons: [1, 2, 3],
    matureContentEnabled: false
  });
  const [lessonState, setLessonState] = useState({
    currentLessonId: 1,
    currentPhase: 1,
    completedPhases: []
  });
  const [isDarkMode, setIsDarkMode] = useState(false);

  // Initialize user data from localStorage
  useEffect(() => {
    const savedData = localStorage.getItem('fluente_user_data');
    if (savedData) {
      const userData = JSON.parse(savedData);
      setUserState(userData);
    }
  }, []);

  // Save user data to localStorage
  useEffect(() => {
    localStorage.setItem('fluente_user_data', JSON.stringify(userState));
  }, [userState]);

  const renderCurrentView = () => {
    switch(currentView) {
      case 'welcome': return <WelcomeScreen setCurrentView={setCurrentView} />;
      case 'signup': return <SignUpScreen setCurrentView={setCurrentView} setUserState={setUserState} />;
      case 'profile': return <ProfileScreen setCurrentView={setCurrentView} setUserState={setUserState} />;
      case 'language': return <LanguageScreen setCurrentView={setCurrentView} setUserState={setUserState} />;
      case 'dashboard': return <Dashboard userState={userState} setCurrentView={setCurrentView} setLessonState={setLessonState} />;
      case 'lesson': return <LessonScreen lessonState={lessonState} setLessonState={setLessonState} setCurrentView={setCurrentView} userState={userState} setUserState={setUserState} isDarkMode={isDarkMode} setIsDarkMode={setIsDarkMode} />;
      case 'completion': return <CompletionScreen setCurrentView={setCurrentView} userState={userState} />;
      default: return <WelcomeScreen setCurrentView={setCurrentView} />;
    }
  };

  return (
    <div className={`app ${isDarkMode ? 'dark-mode' : 'light-mode'}`}>
      {renderCurrentView()}
    </div>
  );
}

// === WELCOME SCREEN ===
function WelcomeScreen({ setCurrentView }) {
  return (
    <div className="welcome-screen">
      <div className="hero-background">
        <div className="gradient-animation"></div>
        <div className="welcome-content">
          <h1 className="logo">Fluente</h1>
          <p className="tagline">Master English like a native</p>
          <div className="welcome-buttons">
            <button className="primary-btn" onClick={() => setCurrentView('signup')}>
              Get Started
            </button>
            <button className="secondary-btn" onClick={() => setCurrentView('dashboard')}>
              I Already Have an Account
            </button>
          </div>
        </div>
      </div>
    </div>
  );
}

// === SIGN UP SCREEN ===
function SignUpScreen({ setCurrentView, setUserState }) {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleSignUp = () => {
    if (email && password) {
      setUserState(prev => ({ ...prev, email }));
      setCurrentView('profile');
    }
  };

  return (
    <div className="signup-screen">
      <div className="form-container">
        <h2>Create Your Account</h2>
        <div className="form-group">
          <input
            type="email"
            placeholder="Email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
            className="form-input"
          />
        </div>
        <div className="form-group">
          <input
            type="password"
            placeholder="Password"
            value={password}
            onChange={(e) => setPassword(e.target.value)}
            className="form-input"
          />
        </div>
        <button className="primary-btn" onClick={handleSignUp}>
          Continua
        </button>
        <div className="social-login">
          <button className="social-btn google">Continue with Google</button>
          <button className="social-btn apple">Continue with Apple</button>
        </div>
      </div>
    </div>
  );
}

// === PROFILE SCREEN ===
function ProfileScreen({ setCurrentView, setUserState }) {
  const [username, setUsername] = useState('');

  const handleProfileSetup = () => {
    if (username) {
      setUserState(prev => ({ 
        ...prev, 
        username,
        userId: Date.now().toString()
      }));
      setCurrentView('language');
    }
  };

  return (
    <div className="profile-screen">
      <div className="form-container">
        <h2>Set Up Your Profile</h2>
        <div className="progress-indicator">2/3</div>
        <div className="form-group">
          <input
            type="text"
            placeholder="Choose a username"
            value={username}
            onChange={(e) => setUsername(e.target.value)}
            className="form-input"
          />
        </div>
        <div className="profile-picture-upload">
          <div className="avatar-placeholder">📷</div>
          <p>Add profile picture (optional)</p>
        </div>
        <button className="primary-btn" onClick={handleProfileSetup}>
          Avanti
        </button>
      </div>
    </div>
  );
}

// === LANGUAGE SCREEN ===
function LanguageScreen({ setCurrentView, setUserState }) {
  const [matureContent, setMatureContent] = useState(false);

  const handleLanguageConfirm = () => {
    setUserState(prev => ({ 
      ...prev, 
      matureContentEnabled: matureContent,
      totalXP: 1247,
      currentStreak: 5
    }));
    setCurrentView('dashboard');
  };

  return (
    <div className="language-screen">
      <div className="form-container">
        <h2>Language Confirmation</h2>
        <div className="progress-indicator">3/3</div>
        <div className="language-selection">
          <p className="italian-text">Parlo italiano, voglio imparare l'inglese</p>
          <p className="english-text">I speak Italian, I want to learn English</p>
        </div>
        <div className="mature-content-checkbox">
          <label>
            <input
              type="checkbox"
              checked={matureContent}
              onChange={(e) => setMatureContent(e.target.checked)}
            />
            Sono maggiorenne (18+) - Enable slang and casual content
          </label>
        </div>
        <button className="primary-btn" onClick={handleLanguageConfirm}>
          Inizia ad Imparare
        </button>
      </div>
    </div>
  );
}

// === DASHBOARD ===
function Dashboard({ userState, setCurrentView, setLessonState }) {
  const lessonCategories = [
    { name: "Basics", italian: "Le Basi", icon: "📚", range: "1-50", lessons: Array.from({length: 50}, (_, i) => i + 1) },
    { name: "Daily Life", italian: "Vita Quotidiana", icon: "🏠", range: "51-150", lessons: Array.from({length: 100}, (_, i) => i + 51) },
    { name: "Conversations", italian: "Conversazioni", icon: "💬", range: "151-300", lessons: Array.from({length: 150}, (_, i) => i + 151) },
    { name: "Business", italian: "Inglese Professionale", icon: "💼", range: "301-500", lessons: Array.from({length: 200}, (_, i) => i + 301) },
    { name: "Slang & Street Talk", italian: "Slang e Linguaggio di Strada", icon: "🔥", range: "501-700", lessons: Array.from({length: 200}, (_, i) => i + 501), mature: true },
    { name: "Gen Z English", italian: "Inglese Generazione Z", icon: "📱", range: "701-850", lessons: Array.from({length: 150}, (_, i) => i + 701), mature: true },
    { name: "Advanced", italian: "Avanzato", icon: "🎓", range: "851-1000", lessons: Array.from({length: 150}, (_, i) => i + 851) }
  ];

  const startLesson = (lessonId) => {
    if (userState.unlockedLessons.includes(lessonId)) {
      setLessonState({
        currentLessonId: lessonId,
        currentPhase: 1,
        completedPhases: []
      });
      setCurrentView('lesson');
    }
  };

  return (
    <div className="dashboard">
      <header className="dashboard-header">
        <div className="user-info">
          <div className="avatar">👤</div>
          <span className="username">{userState.username}</span>
          <div className="stats">
            <span className="streak">🔥 {userState.currentStreak}</span>
            <span className="xp">⭐ {userState.totalXP}</span>
          </div>
        </div>
      </header>

      <div className="progress-overview">
        <h3>Lesson {userState.currentLesson} of 1000 Complete</h3>
        <div className="progress-bar">
          <div className="progress-fill" style={{width: `${(userState.currentLesson/1000)*100}%`}}></div>
        </div>
      </div>

      <div className="leaderboard-widget">
        <div className="glassmorphism-card">
          <h4>Weekly Rank: #5</h4>
          <p>127 active learners</p>
          <button className="view-leaderboard">View Full Leaderboard</button>
        </div>
      </div>

      <div className="lesson-path">
        {lessonCategories.map((category, index) => (
          <div key={index} className="category-section">
            <h3 className="category-header">
              <span className="category-icon">{category.icon}</span>
              {category.name} ({category.range})
              <span className="category-italian">{category.italian}</span>
              {category.mature && !userState.matureContentEnabled && <span className="mature-badge">18+</span>}
            </h3>
            <div className="lesson-grid">
              {category.lessons.slice(0, 20).map(lessonId => (
                <div
                  key={lessonId}
                  className={`lesson-card ${userState.unlockedLessons.includes(lessonId) ? 'unlocked' : 'locked'}`}
                  onClick={() => startLesson(lessonId)}
                >
                  <div className="lesson-number">{lessonId}</div>
                  <div className="lesson-title">Lesson {lessonId}</div>
                  {!userState.unlockedLessons.includes(lessonId) && <div className="lock-icon">🔒</div>}
                </div>
              ))}
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}

// === LESSON SCREEN ===
function LessonScreen({ lessonState, setLessonState, setCurrentView, userState, setUserState, setIsDarkMode }) {
  const [selectedAnswer, setSelectedAnswer] = useState('');
  const [isRecording, setIsRecording] = useState(false);
  const [recordingText, setRecordingText] = useState('');

  useEffect(() => {
    setIsDarkMode(true);
    return () => setIsDarkMode(false);
  }, [setIsDarkMode]);

  const sampleLessonData = {
    1: { word: "Hello", italian: "Ciao", image: "👋", pronunciation: "/həˈloʊ/" },
    2: { word: "Thank you", italian: "Grazie", image: "🙏", pronunciation: "/θæŋk juː/" },
    3: { word: "Good morning", italian: "Buongiorno", image: "🌅", pronunciation: "/ɡʊd ˈmɔːrnɪŋ/" }
  };

  const currentLessonData = sampleLessonData[lessonState.currentLessonId] || sampleLessonData[1];

  const nextPhase = () => {
    if (lessonState.currentPhase < 5) {
      setLessonState(prev => ({
        ...prev,
        currentPhase: prev.currentPhase + 1,
        completedPhases: [...prev.completedPhases, prev.currentPhase]
      }));
    } else {
      completeLesson();
    }
  };

  const completeLesson = () => {
    setUserState(prev => ({
      ...prev,
      totalXP: prev.totalXP + 50,
      currentLesson: prev.currentLesson + 1,
      unlockedLessons: [...prev.unlockedLessons, prev.currentLesson + 1]
    }));
    setCurrentView('completion');
  };

  const startRecording = () => {
    setIsRecording(true);
    // Simulate voice recognition
    setTimeout(() => {
      setRecordingText(currentLessonData.word);
      setIsRecording(false);
    }, 2000);
  };

  const renderPhase = () => {
    switch(lessonState.currentPhase) {
      case 1:
        return (
          <div className="phase-content exposure">
            <h2>Phase 1: Exposure</h2>
            <div className="word-display">
              <div className="word-image">{currentLessonData.image}</div>
              <h1 className="english-word">{currentLessonData.word}</h1>
              <p className="pronunciation">{currentLessonData.pronunciation}</p>
              <h2 className="italian-translation">{currentLessonData.italian}</h2>
              <button className="audio-btn">🔊 Listen</button>
            </div>
            <button className="next-btn" onClick={nextPhase}>Next</button>
          </div>
        );

      case 2:
        return (
          <div className="phase-content comprehension">
            <h2>Phase 2: Comprehension</h2>
            <p className="question">Cosa significa "{currentLessonData.word}"?</p>
            <div className="multiple-choice">
              {[currentLessonData.italian, "Arrivederci", "Prego", "Scusa"].map((option, index) => (
                <button
                  key={index}
                  className={`choice-btn ${selectedAnswer === option ? 'selected' : ''}`}
                  onClick={() => setSelectedAnswer(option)}
                >
                  {option}
                </button>
              ))}
            </div>
            <button className="next-btn" onClick={nextPhase} disabled={!selectedAnswer}>Next</button>
          </div>
        );

      case 3:
        return (
          <div className="phase-content construction">
            <h2>Phase 3: Construction</h2>
            <p className="instruction">Costruisci la frase (Build the sentence)</p>
            <div className="sentence-builder">
              <div className="word-blocks">
                {currentLessonData.word.split(' ').map((word, index) => (
                  <div key={index} className="word-block">{word}</div>
                ))}
              </div>
            </div>
            <button className="next-btn" onClick={nextPhase}>Next</button>
          </div>
        );

      case 4:
        return (
          <div className="phase-content production">
            <h2>Phase 4: Production</h2>
            <p className="instruction">Pronuncia: {currentLessonData.word}</p>
            <div className="voice-interface">
              <button
                className={`microphone-btn ${isRecording ? 'recording' : ''}`}
                onClick={startRecording}
                disabled={isRecording}
              >
                🎤
              </button>
              {isRecording && <div className="recording-animation">Recording...</div>}
              {recordingText && (
                <div className="recognition-result">
                  <p>You said: "{recordingText}"</p>
                  <div className="confidence-score">Confidence: 92%</div>
                </div>
              )}
            </div>
            <button className="next-btn" onClick={nextPhase}>Next</button>
          </div>
        );

      case 5:
        return (
          <div className="phase-content review">
            <h2>Phase 5: Review</h2>
            <p className="question">Traduci: {currentLessonData.italian}</p>
            <input
              type="text"
              className="translation-input"
              placeholder="Type your answer..."
            />
            <button className="next-btn" onClick={nextPhase}>Complete Lesson</button>
          </div>
        );

      default:
        return <div>Loading...</div>;
    }
  };

  return (
    <div className="lesson-screen dark-mode">
      <div className="cosmic-background"></div>
      <header className="lesson-header">
        <div className="phase-indicator">
          {[1,2,3,4,5].map(phase => (
            <div
              key={phase}
              className={`phase-dot ${phase === lessonState.currentPhase ? 'active' : ''} ${lessonState.completedPhases.includes(phase) ? 'completed' : ''}`}
            ></div>
          ))}
        </div>
        <button className="exit-btn" onClick={() => setCurrentView('dashboard')}>✕</button>
      </header>
      <main className="lesson-content">
        {renderPhase()}
      </main>
    </div>
  );
}

// === COMPLETION SCREEN ===
function CompletionScreen({ setCurrentView, userState }) {
  return (
    <div className="completion-screen">
      <div className="celebration-content">
        <div className="checkmark-animation">✅</div>
        <h1>Lezione Completata!</h1>
        <div className="xp-earned">+50 XP</div>
        <div className="achievement">
          <p>New Achievement Unlocked!</p>
          <div className="badge">🏆 Lesson Master</div>
        </div>
        <div className="progress-update">
          <p>{userState.currentLesson} of 1000 lessons complete</p>
        </div>
        <button className="continue-btn" onClick={() => setCurrentView('dashboard')}>
          Continua il Percorso
        </button>
      </div>
    </div>
    /* === FLUENTE APP STYLES === */

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  line-height: 1.6;
  color: #212529;
}

.app {
  min-height: 100vh;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

/* === LIGHT MODE === */
.light-mode {
  background: #f8f9fa;
  color: #212529;
}

/* === DARK MODE === */
.dark-mode {
  background: linear-gradient(-45deg, #1a1d29, #2d1b69, #1a1d29, #2d1b69);
  background-size: 400% 400%;
  animation: gradientShift 15s ease infinite;
  color: #ffffff;
}

@keyframes gradientShift {
  0% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}

/* === WELCOME SCREEN === */
.welcome-screen {
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  overflow: hidden;
}

.hero-background {
  width: 100%;
  height: 100%;
  background: linear-gradient(-45deg, #667eea, #764ba2, #f093fb, #f5576c);
  background-size: 400% 400%;
  animation: gradientShift 15s ease infinite;
  display: flex;
  align-items: center;
  justify-content: center;
}

.welcome-content {
  text-align: center;
  color: white;
  z-index: 2;
}

.logo {
  font-size: 4rem;
  font-weight: 900;
  margin-bottom: 1rem;
  text-shadow: 0 4px 8px rgba(0,0,0,0.3);
}

.tagline {
  font-size: 1.2rem;
  margin-bottom: 3rem;
  opacity: 0.9;
}

.welcome-buttons {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  align-items: center;
}

.primary-btn {
  background: #00ff88;
  color: #1a1d29;
  border: none;
  padding: 1rem 2rem;
  border-radius: 50px;
  font-size: 1.1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  min-width: 200px;
  min-height: 44px;
}

.primary-btn:hover {
  transform: scale(1.05);
  box-shadow: 0 8px 25px rgba(0,255,136,0.3);
}

.secondary-btn {
  background: transparent;
  color: white;
  border: 2px solid rgba(255,255,255,0.3);
  padding: 1rem 2rem;
  border-radius: 50px;
  font-size: 1.1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  min-width: 200px;
  min-height: 44px;
}

.secondary-btn:hover {
  background: rgba(255,255,255,0.1);
  transform: scale(1.05);
}

/* === FORM SCREENS === */
.signup-screen,
.profile-screen,
.language-screen {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #f8f9fa;
  padding: 2rem;
}

.form-container {
  background: white;
  padding: 3rem;
  border-radius: 20px;
  box-shadow: 0 10px 40px rgba(0,0,0,0.1);
  width: 100%;
  max-width: 400px;
}

.form-container h2 {
  text-align: center;
  margin-bottom: 2rem;
  color: #1a1d29;
  font-size: 1.8rem;
}

.progress-indicator {
  text-align: center;
  color: #6c757d;
  margin-bottom: 2rem;
  font-weight: 500;
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-input {
  width: 100%;
  padding: 1rem;
  border: 2px solid #e9ecef;
  border-radius: 12px;
  font-size: 1rem;
  transition: border-color 0.3s ease;
  min-height: 44px;
}

.form-input:focus {
  outline: none;
  border-color: #00ff88;
}

.social-login {
  margin-top: 2rem;
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.social-btn {
  width: 100%;
  padding: 1rem;
  border: 2px solid #e9ecef;
  border-radius: 12px;
  background: white;
  font-size: 1rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
  min-height: 44px;
}

.social-btn:hover {
  border-color: #00ff88;
  transform: translateY(-2px);
}

.profile-picture-upload {
  text-align: center;
  margin: 2rem 0;
}

.avatar-placeholder {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  background: #e9ecef;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2rem;
  margin: 0 auto 1rem;
  cursor: pointer;
  transition: all 0.3s ease;
}

.avatar-placeholder:hover {
  background: #00ff88;
  transform: scale(1.1);
}

.language-selection {
  text-align: center;
  margin: 2rem 0;
  padding: 2rem;
  background: #f8f9fa;
  border-radius: 12px;
}

.italian-text {
  font-size: 1.2rem;
  font-weight: 600;
  color: #1a1d29;
  margin-bottom: 0.5rem;
}

.english-text {
  color: #6c757d;
  font-style: italic;
}

.mature-content-checkbox {
  margin: 2rem 0;
  padding: 1rem;
  background: #fff3cd;
  border-radius: 8px;
  border-left: 4px solid #ffc107;
}

.mature-content-checkbox label {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  cursor: pointer;
}

/* === DASHBOARD === */
.dashboard {
  min-height: 100vh;
  background: #f8f9fa;
  padding: 1rem;
}

.dashboard-header {
  background: white;
  padding: 1rem 2rem;
  border-radius: 20px;
  box-shadow: 0 4px 15px rgba(0,0,0,0.1);
  margin-bottom: 2rem;
}

.user-info {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.avatar {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background: #00ff88;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.5rem;
}

.username {
  font-size: 1.2rem;
  font-weight: 600;
  flex: 1;
}

.stats {
  display: flex;
  gap: 1rem;
}

.streak,
.xp {
  background: #e9ecef;
  padding: 0.5rem 1rem;
  border-radius: 20px;
  font-weight: 500;
}

.progress-overview {
  background: white;
  padding: 2rem;
  border-radius: 20px;
  box-shadow: 0 4px 15px rgba(0,0,0,0.1);
  margin-bottom: 2rem;
  text-align: center;
}

.progress-overview h3 {
  margin-bottom: 1rem;
  color: #1a1d29;
}

.progress-bar {
  width: 100%;
  height: 10px;
  background: #e9ecef;
  border-radius: 5px;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #00ff88, #00d4aa);
  transition: width 0.5s ease;
}

.leaderboard-widget {
  margin-bottom: 2rem;
}

.glassmorphism-card {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.2);
  border-radius: 20px;
  padding: 2rem;
  text-align: center;
  color: #1a1d29;
  background: white;
  box-shadow: 0 8px 32px rgba(0,0,0,0.1);
}

.glassmorphism-card h4 {
  margin-bottom: 0.5rem;
  font-size: 1.3rem;
}

.view-leaderboard {
  background: #007bff;
  color: white;
  border: none;
  padding: 0.8rem 1.5rem;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s ease;
  margin-top: 1rem;
}

.view-leaderboard:hover {
  background: #0056b3;
  transform: translateY(-2px);
}

.lesson-path {
  background: white;
  border-radius: 20px;
  padding: 2rem;
  box-shadow: 0 4px 15px rgba(0,0,0,0.1);
}

.category-section {
  margin-bottom: 3rem;
}

.category-header {
  display: flex;
  align-items: center;
  gap: 1rem;
  );
}

export default App;

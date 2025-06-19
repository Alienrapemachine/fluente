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
  );
}

export default App;

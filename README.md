# SCT_WD_03

import React, { useState } from 'react';
import { CheckCircle, XCircle, Trophy, RotateCcw } from 'lucide-react';

const QuizGame = () => {
  const questions = [
    {
      id: 1,
      type: 'single',
      question: 'What is the capital of France?',
      options: ['London', 'Berlin', 'Paris', 'Madrid'],
      correctAnswer: 'Paris'
    },
    {
      id: 2,
      type: 'multiple',
      question: 'Which of these are programming languages? (Select all that apply)',
      options: ['Python', 'HTML', 'JavaScript', 'CSS'],
      correctAnswers: ['Python', 'JavaScript']
    },
    {
      id: 3,
      type: 'fill',
      question: 'The largest planet in our solar system is _____.',
      correctAnswer: 'Jupiter',
      placeholder: 'Type your answer'
    },
    {
      id: 4,
      type: 'single',
      question: 'What year did World War II end?',
      options: ['1943', '1944', '1945', '1946'],
      correctAnswer: '1945'
    },
    {
      id: 5,
      type: 'multiple',
      question: 'Which of these are prime numbers? (Select all that apply)',
      options: ['2', '4', '7', '9', '11'],
      correctAnswers: ['2', '7', '11']
    },
    {
      id: 6,
      type: 'fill',
      question: 'H2O is the chemical formula for _____.',
      correctAnswer: 'Water',
      placeholder: 'Type your answer'
    },
    {
      id: 7,
      type: 'single',
      question: 'Who wrote "Romeo and Juliet"?',
      options: ['Charles Dickens', 'William Shakespeare', 'Jane Austen', 'Mark Twain'],
      correctAnswer: 'William Shakespeare'
    },
    {
      id: 8,
      type: 'fill',
      question: 'The process by which plants make their own food is called _____.',
      correctAnswer: 'Photosynthesis',
      placeholder: 'Type your answer'
    }
  ];

  const [currentQuestion, setCurrentQuestion] = useState(0);
  const [selectedAnswers, setSelectedAnswers] = useState({});
  const [fillAnswers, setFillAnswers] = useState({});
  const [showResults, setShowResults] = useState(false);
  const [score, setScore] = useState(0);

  const handleSingleChoice = (answer) => {
    setSelectedAnswers({
      ...selectedAnswers,
      [currentQuestion]: answer
    });
  };

  const handleMultipleChoice = (answer) => {
    const current = selectedAnswers[currentQuestion] || [];
    const updated = current.includes(answer)
      ? current.filter(a => a !== answer)
      : [...current, answer];
    
    setSelectedAnswers({
      ...selectedAnswers,
      [currentQuestion]: updated
    });
  };

  const handleFillAnswer = (value) => {
    setFillAnswers({
      ...fillAnswers,
      [currentQuestion]: value
    });
  };

  const calculateScore = () => {
    let points = 0;
    questions.forEach((q, index) => {
      if (q.type === 'single') {
        if (selectedAnswers[index] === q.correctAnswer) {
          points++;
        }
      } else if (q.type === 'multiple') {
        const userAnswers = selectedAnswers[index] || [];
        const correct = q.correctAnswers.sort().toString();
        const user = userAnswers.sort().toString();
        if (correct === user) {
          points++;
        }
      } else if (q.type === 'fill') {
        const userAnswer = (fillAnswers[index] || '').trim().toLowerCase();
        const correctAnswer = q.correctAnswer.toLowerCase();
        if (userAnswer === correctAnswer) {
          points++;
        }
      }
    });
    setScore(points);
    setShowResults(true);
  };

  const handleNext = () => {
    if (currentQuestion < questions.length - 1) {
      setCurrentQuestion(currentQuestion + 1);
    } else {
      calculateScore();
    }
  };

  const handlePrevious = () => {
    if (currentQuestion > 0) {
      setCurrentQuestion(currentQuestion - 1);
    }
  };

  const restartQuiz = () => {
    setCurrentQuestion(0);
    setSelectedAnswers({});
    setFillAnswers({});
    setShowResults(false);
    setScore(0);
  };

  const isAnswered = () => {
    const q = questions[currentQuestion];
    if (q.type === 'single') {
      return selectedAnswers[currentQuestion] !== undefined;
    } else if (q.type === 'multiple') {
      return (selectedAnswers[currentQuestion] || []).length > 0;
    } else if (q.type === 'fill') {
      return (fillAnswers[currentQuestion] || '').trim() !== '';
    }
    return false;
  };

  const getAnswerStatus = (questionIndex) => {
    const q = questions[questionIndex];
    if (q.type === 'single') {
      return selectedAnswers[questionIndex] === q.correctAnswer;
    } else if (q.type === 'multiple') {
      const userAnswers = selectedAnswers[questionIndex] || [];
      const correct = q.correctAnswers.sort().toString();
      const user = userAnswers.sort().toString();
      return correct === user;
    } else if (q.type === 'fill') {
      const userAnswer = (fillAnswers[questionIndex] || '').trim().toLowerCase();
      const correctAnswer = q.correctAnswer.toLowerCase();
      return userAnswer === correctAnswer;
    }
    return false;
  };

  if (showResults) {
    const percentage = Math.round((score / questions.length) * 100);
    
    return (
      <div className="min-h-screen bg-gradient-to-br from-purple-500 via-pink-500 to-red-500 p-8 flex items-center justify-center">
        <div className="bg-white rounded-3xl shadow-2xl p-8 max-w-3xl w-full">
          <div className="text-center mb-8">
            <Trophy className="w-24 h-24 text-yellow-500 mx-auto mb-4" />
            <h1 className="text-4xl font-bold text-gray-800 mb-2">Quiz Complete!</h1>
            <p className="text-gray-600 text-lg">Here's how you did:</p>
          </div>

          <div className="bg-gradient-to-r from-purple-100 to-pink-100 rounded-2xl p-8 mb-8">
            <div className="text-center mb-6">
              <div className="text-6xl font-bold text-purple-600 mb-2">
                {score}/{questions.length}
              </div>
              <div className="text-2xl text-gray-700">
                {percentage}% Correct
              </div>
            </div>
            
            <div className="w-full bg-gray-200 rounded-full h-4 overflow-hidden">
              <div 
                className="bg-gradient-to-r from-green-400 to-green-600 h-4 rounded-full transition-all duration-1000"
                style={{ width: `${percentage}%` }}
              />
            </div>
          </div>

          <div className="space-y-4 mb-8">
            <h3 className="text-xl font-semibold text-gray-800 mb-4">Question Review:</h3>
            {questions.map((q, index) => (
              <div key={q.id} className="flex items-center gap-3 p-4 bg-gray-50 rounded-lg">
                {getAnswerStatus(index) ? (
                  <CheckCircle className="w-6 h-6 text-green-500 flex-shrink-0" />
                ) : (
                  <XCircle className="w-6 h-6 text-red-500 flex-shrink-0" />
                )}
                <div className="flex-1">
                  <p className="text-gray-700 font-medium">Question {index + 1}</p>
                  <p className="text-sm text-gray-500">{q.question}</p>
                </div>
              </div>
            ))}
          </div>

          <button
            onClick={restartQuiz}
            className="w-full bg-gradient-to-r from-purple-600 to-pink-600 text-white py-4 rounded-xl font-semibold text-lg hover:from-purple-700 hover:to-pink-700 transition-all shadow-lg flex items-center justify-center gap-2"
          >
            <RotateCcw className="w-5 h-5" />
            Take Quiz Again
          </button>
        </div>
      </div>
    );
  }

  const currentQ = questions[currentQuestion];
  const progress = ((currentQuestion + 1) / questions.length) * 100;

  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-500 via-purple-500 to-pink-500 p-8 flex items-center justify-center">
      <div className="bg-white rounded-3xl shadow-2xl p-8 max-w-2xl w-full">
        {/* Header */}
        <div className="mb-8">
          <div className="flex justify-between items-center mb-4">
            <h1 className="text-3xl font-bold text-gray-800">Quiz Game</h1>
            <span className="text-lg font-semibold text-purple-600">
              Question {currentQuestion + 1}/{questions.length}
            </span>
          </div>
          
          {/* Progress Bar */}
          <div className="w-full bg-gray-200 rounded-full h-3 overflow-hidden">
            <div 
              className="bg-gradient-to-r from-blue-500 to-purple-600 h-3 rounded-full transition-all duration-300"
              style={{ width: `${progress}%` }}
            />
          </div>
        </div>

        {/* Question Type Badge */}
        <div className="mb-4">
          <span className="inline-block px-4 py-1 bg-purple-100 text-purple-700 rounded-full text-sm font-semibold">
            {currentQ.type === 'single' && 'Single Choice'}
            {currentQ.type === 'multiple' && 'Multiple Choice (Select All)'}
            {currentQ.type === 'fill' && 'Fill in the Blank'}
          </span>
        </div>

        {/* Question */}
        <div className="mb-8">
          <h2 className="text-2xl font-semibold text-gray-800 mb-6">
            {currentQ.question}
          </h2>

          {/* Single Choice */}
          {currentQ.type === 'single' && (
            <div className="space-y-3">
              {currentQ.options.map((option, index) => (
                <button
                  key={index}
                  onClick={() => handleSingleChoice(option)}
                  className={`w-full p-4 rounded-xl text-left transition-all ${
                    selectedAnswers[currentQuestion] === option
                      ? 'bg-purple-600 text-white shadow-lg scale-105'
                      : 'bg-gray-100 text-gray-800 hover:bg-gray-200'
                  }`}
                >
                  <div className="flex items-center gap-3">
                    <div className={`w-6 h-6 rounded-full border-2 flex items-center justify-center ${
                      selectedAnswers[currentQuestion] === option
                        ? 'border-white bg-white'
                        : 'border-gray-400'
                    }`}>
                      {selectedAnswers[currentQuestion] === option && (
                        <div className="w-3 h-3 rounded-full bg-purple-600" />
                      )}
                    </div>
                    <span className="font-medium">{option}</span>
                  </div>
                </button>
              ))}
            </div>
          )}

          {/* Multiple Choice */}
          {currentQ.type === 'multiple' && (
            <div className="space-y-3">
              {currentQ.options.map((option, index) => {
                const isSelected = (selectedAnswers[currentQuestion] || []).includes(option);
                return (
                  <button
                    key={index}
                    onClick={() => handleMultipleChoice(option)}
                    className={`w-full p-4 rounded-xl text-left transition-all ${
                      isSelected
                        ? 'bg-purple-600 text-white shadow-lg scale-105'
                        : 'bg-gray-100 text-gray-800 hover:bg-gray-200'
                    }`}
                  >
                    <div className="flex items-center gap-3">
                      <div className={`w-6 h-6 rounded border-2 flex items-center justify-center ${
                        isSelected
                          ? 'border-white bg-white'
                          : 'border-gray-400'
                      }`}>
                        {isSelected && (
                          <CheckCircle className="w-5 h-5 text-purple-600" />
                        )}
                      </div>
                      <span className="font-medium">{option}</span>
                    </div>
                  </button>
                );
              })}
            </div>
          )}

          {/* Fill in the Blank */}
          {currentQ.type === 'fill' && (
            <input
              type="text"
              value={fillAnswers[currentQuestion] || ''}
              onChange={(e) => handleFillAnswer(e.target.value)}
              placeholder={currentQ.placeholder}
              className="w-full p-4 border-2 border-gray-300 rounded-xl text-lg focus:border-purple-600 focus:outline-none transition-colors"
            />
          )}
        </div>

        {/* Navigation */}
        <div className="flex gap-4">
          <button
            onClick={handlePrevious}
            disabled={currentQuestion === 0}
            className={`px-6 py-3 rounded-xl font-semibold transition-all ${
              currentQuestion === 0
                ? 'bg-gray-200 text-gray-400 cursor-not-allowed'
                : 'bg-gray-300 text-gray-700 hover:bg-gray-400'
            }`}
          >
            Previous
          </button>
          
          <button
            onClick={handleNext}
            disabled={!isAnswered()}
            className={`flex-1 py-3 rounded-xl font-semibold transition-all ${
              !isAnswered()
                ? 'bg-gray-200 text-gray-400 cursor-not-allowed'
                : currentQuestion === questions.length - 1
                ? 'bg-gradient-to-r from-green-500 to-green-600 text-white hover:from-green-600 hover:to-green-700 shadow-lg'
                : 'bg-gradient-to-r from-purple-600 to-pink-600 text-white hover:from-purple-700 hover:to-pink-700 shadow-lg'
            }`}
          >
            {currentQuestion === questions.length - 1 ? 'Submit Quiz' : 'Next Question'}
          </button>
        </div>
      </div>
    </div>
  );
};

export default QuizGame;

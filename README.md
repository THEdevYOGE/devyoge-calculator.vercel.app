import React, { useState } from "react";

export default function SuperNiceCalculator() {
  const [display, setDisplay] = useState("0");
  const [history, setHistory] = useState([]);

  const appendValue = (value) => {
    if (display === "0" && value !== ".") {
      setDisplay(value);
    } else {
      setDisplay((prev) => prev + value);
    }
  };

  const clearDisplay = () => {
    setDisplay("0");
  };

  const deleteLast = () => {
    if (display.length <= 1) {
      setDisplay("0");
    } else {
      setDisplay(display.slice(0, -1));
    }
  };

  const scientificFunction = (type) => {
    try {
      const num = parseFloat(display);

      switch (type) {
        case "sqrt":
          setDisplay(Math.sqrt(num).toString());
          break;
        case "square":
          setDisplay((num * num).toString());
          break;
        case "sin":
          setDisplay(Math.sin(num).toString());
          break;
        case "cos":
          setDisplay(Math.cos(num).toString());
          break;
        case "tan":
          setDisplay(Math.tan(num).toString());
          break;
        case "log":
          setDisplay(Math.log10(num).toString());
          break;
        case "pi":
          setDisplay(Math.PI.toString());
          break;
        default:
          break;
      }
    } catch {
      setDisplay("Error");
    }
  };

  const calculate = () => {
    try {
      const sanitized = display.replace(/×/g, "*").replace(/÷/g, "/");
      const result = Function(`return ${sanitized}`)();

      if (result === undefined || Number.isNaN(result)) {
        setDisplay("Error");
        return;
      }

      const finalResult = result.toString();

      setHistory((prev) => [
        { expression: display, result: finalResult },
        ...prev.slice(0, 5),
      ]);

      setDisplay(finalResult);
    } catch {
      setDisplay("Error");
    }
  };

  const buttons = [
    "7", "8", "9", "÷",
    "4", "5", "6", "×",
    "1", "2", "3", "-",
    "0", ".", "+", "=",
  ];

  const scientificButtons = [
    { label: "√", action: () => scientificFunction("sqrt") },
    { label: "x²", action: () => scientificFunction("square") },
    { label: "sin", action: () => scientificFunction("sin") },
    { label: "cos", action: () => scientificFunction("cos") },
    { label: "tan", action: () => scientificFunction("tan") },
    { label: "log", action: () => scientificFunction("log") },
    { label: "π", action: () => scientificFunction("pi") },
    { label: "%", action: () => appendValue("%") },
  ];

  return (
    <div className="min-h-screen flex items-center justify-center bg-black overflow-hidden relative p-4">
      <div className="absolute inset-0 bg-[radial-gradient(circle_at_center,_rgba(255,0,0,0.18),_transparent_60%)]"></div>

      <div className="absolute w-[700px] h-[700px] border border-red-500/20 rounded-full"></div>
      <div className="absolute w-[500px] h-[500px] border border-red-500/20 rounded-full"></div>

      <div className="absolute inset-0 opacity-20">
        <div className="absolute top-1/2 left-1/2 w-[2px] h-full bg-red-500 transform -translate-x-1/2 -translate-y-1/2"></div>
        <div className="absolute top-1/2 left-1/2 h-[2px] w-full bg-red-500 transform -translate-x-1/2 -translate-y-1/2"></div>
      </div>

      <div className="relative z-10 w-full max-w-md bg-black/60 backdrop-blur-2xl rounded-3xl border border-red-500/30 shadow-[0_0_40px_rgba(255,0,0,0.3)] overflow-hidden">
        <div className="p-6">
          <div className="flex items-center justify-center mb-5">
            <div className="w-14 h-14 rounded-full bg-red-600 flex items-center justify-center shadow-lg shadow-red-500/40 text-3xl">
              🕷️
            </div>
          </div>

          <h1 className="text-4xl font-extrabold text-center text-red-500 tracking-widest mb-6">
            devYOGE
          </h1>

          <div className="bg-black/70 rounded-3xl p-5 border border-red-500/20 mb-5">
            <div className="text-right text-gray-400 text-sm min-h-[20px]">
              {history[0]?.expression || "Spider Mode Activated"}
            </div>

            <div className="text-right text-white text-5xl font-bold mt-3 break-all overflow-hidden">
              {display}
            </div>
          </div>

          <div className="grid grid-cols-4 gap-3 mb-4">
            {scientificButtons.map((btn, index) => (
              <button
                key={index}
                onClick={btn.action}
                className="bg-red-500/20 hover:bg-red-500/40 text-red-300 rounded-2xl p-3 font-bold transition-all duration-200 hover:scale-105"
              >
                {btn.label}
              </button>
            ))}
          </div>

          <div className="grid grid-cols-4 gap-3">
            <button
              onClick={clearDisplay}
              className="col-span-2 bg-red-600 hover:bg-red-700 text-white rounded-2xl p-4 text-xl font-bold transition-all hover:scale-105"
            >
              AC
            </button>

            <button
              onClick={deleteLast}
              className="bg-yellow-500 hover:bg-yellow-600 text-white rounded-2xl p-4 text-xl font-bold transition-all hover:scale-105"
            >
              ⌫
            </button>

            <button
              onClick={() => appendValue("÷")}
              className="bg-red-500 hover:bg-red-600 text-white rounded-2xl p-4 text-xl font-bold transition-all hover:scale-105"
            >
              ÷
            </button>

            {buttons.map((btn, index) => (
              <button
                key={index}
                onClick={() =>
                  btn === "=" ? calculate() : appendValue(btn)
                }
                className={`rounded-2xl p-4 text-xl font-bold transition-all duration-200 hover:scale-105 ${
                  ["÷", "×", "-", "+", "="].includes(btn)
                    ? "bg-red-500 hover:bg-red-600 text-white"
                    : "bg-white/10 hover:bg-white/20 text-white"
                }`}
              >
                {btn}
              </button>
            ))}
          </div>

          <div className="mt-6">
            <h2 className="text-red-400 text-lg font-bold mb-3 tracking-wide">
              History
            </h2>

            <div className="space-y-2 max-h-40 overflow-y-auto pr-1">
              {history.length === 0 ? (
                <p className="text-gray-400 text-sm">No calculations yet</p>
              ) : (
                history.map((item, idx) => (
                  <div
                    key={idx}
                    className="bg-white/5 border border-red-500/10 rounded-2xl p-3 flex justify-between text-white"
                  >
                    <span className="truncate max-w-[60%]">{item.expression}</span>
                    <span className="font-bold text-red-400">{item.result}</span>
                  </div>
                ))
              )}
            </div>
          </div>
        </div>
      </div>
    </div>
  );
}


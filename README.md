
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Lunari Club 🌙</title>
<style>
* { box-sizing: border-box; }

:root {
  --pink: #ffc6df;
  --purple: #c9b6ff;
  --dark: #302344;
  --paper: #fff7fc;
}

body {
  margin: 0;
  padding: 20px 14px 40px;
  background: #f5eaff;
  color: var(--dark);
  font-family: "Courier New", monospace;
  image-rendering: pixelated;
}

.app {
  max-width: 520px;
  margin: auto;
}

header {
  text-align: center;
  padding: 22px 12px;
  background: var(--dark);
  color: white;
  border: 4px solid #fff;
  box-shadow: 6px 6px 0 var(--purple);
}

h1 {
  font-size: clamp(28px, 8vw, 42px);
  margin: 8px 0;
}

header p { color: var(--pink); }

.pixel {
  font-size: 27px;
  letter-spacing: 5px;
}

.card {
  background: var(--paper);
  border: 3px solid var(--dark);
  padding: 17px;
  margin-top: 20px;
  box-shadow: 4px 4px 0 var(--purple);
}

h2 { font-size: 19px; margin-top: 0; }

label {
  display: block;
  margin: 12px 0 6px;
  font-weight: bold;
}

input, textarea {
  width: 100%;
  padding: 13px;
  border: 2px solid #b7a0e8;
  background: white;
  color: var(--dark);
  font: inherit;
  border-radius: 4px;
  font-size: 16px;
}

button {
  padding: 12px;
  border: 2px solid var(--dark);
  background: var(--pink);
  color: var(--dark);
  font: bold 14px "Courier New", monospace;
  cursor: pointer;
  box-shadow: 3px 3px 0 var(--dark);
}

button:active {
  transform: translate(2px, 2px);
  box-shadow: 1px 1px 0 var(--dark);
}

button:focus-visible, input:focus-visible,
textarea:focus-visible {
  outline: 3px solid #8a68ce;
  outline-offset: 3px;
}

.buttons {
  display: flex;
  gap: 10px;
  margin-top: 12px;
}

.buttons button { flex: 1; }

#result {
  margin-top: 16px;
  padding: 14px;
  background: #eee5ff;
  overflow-wrap: anywhere;
  min-height: 50px;
  line-height: 1.7;
}

.word {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 10px;
  padding: 10px 0;
  border-bottom: 1px dashed #c9b6ff;
}

.word strong { color: #8055b9; }
.word button { padding: 7px; }

.small {
  font-size: 12px;
  line-height: 1.6;
  color: #685778;
}

footer {
  text-align: center;
  margin-top: 24px;
  font-size: 12px;
}
</style>
</head>

<body>
<div class="app">

<header>
  <div class="pixel">☾ ✦ ♡ ✦ ☽</div>
  <h1>Lunari Club</h1>
  <p>your little secret universe</p>
  <div>✧ friends only ✧</div>
</header>

<section class="card">
  <h2>🌙 Secret Translator</h2>
  <p class="small">
    Type words separated by spaces. Known words
    will be translated using your dictionary.
  </p>
  <label for="message">Your message</label>
  <textarea id="message" rows="3"
    placeholder="Try: mi luma ti"></textarea>
  <div class="buttons">
    <button onclick="translateText()">✧ Translate</button>
    <button onclick="copyResult()">♡ Copy</button>
  </div>
  <div id="result" aria-live="polite">
    Your Lunari message appears here... 🌸
  </div>
</section>

<section class="card">
  <h2>📖 Secret Dictionary</h2>
  <input id="search" placeholder="🔎 Find a word..."
    oninput="renderWords()">
  <div id="dictionary"></div>
</section>

<section class="card">
  <h2>✨ Invent a secret word</h2>
  <label for="newWord">Lunari word</label>
  <input id="newWord" placeholder="e.g. veluni">
  <label for="meaning">Meaning in English or Darija</label>
  <input id="meaning" placeholder="e.g. come quickly">
  <div class="buttons">
    <button onclick="addWord()">＋ Add word</button>
  </div>
  <p id="notice" class="small" aria-live="polite"></p>
</section>

<footer>
  Made with ♡ by the Lunari friend group<br>
  ☾ keep creating your own words ☽
</footer>

</div>

<script>
// Your starter Lunari dictionary.
// Add or change words inside these braces.
const words = {
  "miu": "I / me",
  "tae": "you",
  "zari": "friend",
  "veya": "bestie",
  "noxi": "hello",
  "veko": "where",
  "toki": "what",
  "kiri": "yes / okay",
  "noku": "no",
  "senu": "wait",
  "rika": "come here",
  "dovi": "go / leave",
  "shavi": "be quiet",
  "zuno": "teacher",
  "pika": "food",
  "foni": "phone",
  "darsa": "class",
  "homi": "home",
  "zuni": "now",
  "vashi": "later",
  "mishi": "funny",
  "kova": "weird",
  "fera": "happy",
  "moka": "tired",
  "daku": "annoying",
  "luma": "love",
  "leth": "secret",
  "vora": "suspicious",
  "vani": "look",
  "nara": "today",
  "lomi": "I want",
  "neku": "here",
  "syl": "forest",
  "nox": "night",
  "vael": "star"
};

function translateText() {
  const text = document.getElementById("message")
    .value.trim().toLowerCase();

  if (!text) {
    document.getElementById("result").textContent =
      "Write a message first! 🌷";
    return;
  }

  const result = text.split(/\s+/).map(token => {
    // Keep punctuation attached to words.
    const match = token.match(
      /^([^a-z0-9]*)([a-z0-9'-]+)([^a-z0-9]*)$/i
    );

    if (!match) return token;

    const [, before, word, after] = match;
    const meaning = words[word.toLowerCase()];

    return meaning
      ? before + meaning + after
      : token;
  }).join(" ");

  document.getElementById("result").textContent = result;
}

async function copyResult() {
  const text = document.getElementById("result")
    .textContent;

  try {
    await navigator.clipboard.writeText(text);
    document.getElementById("result").textContent =
      text + "\n♡ Copied!";
  } catch {
    document.getElementById("notice").textContent =
      "Copy unavailable here. Select the result and copy it.";
  }
}

function renderWords() {
  const query = document.getElementById("search")
    .value.trim().toLowerCase();

  const list = document.getElementById("dictionary");
  list.replaceChildren();

  Object.entries(words)
    .filter(([word, meaning]) =>
      word.includes(query) ||
      meaning.toLowerCase().includes(query)
    )
    .sort(([a], [b]) => a.localeCompare(b))
    .forEach(([word, meaning]) => {
      const row = document.createElement("div");
      row.className = "word";

      const info = document.createElement("div");
      const strong = document.createElement("strong");
      strong.textContent = word;

      const description = document.createElement("div");
      description.textContent = meaning;
      description.className = "small";

      info.append(strong, description);

      const button = document.createElement("button");
      button.textContent = "Use ↗";
      button.setAttribute("aria-label", "Use " + word);
      button.onclick = () => {
        document.getElementById("message").value +=
          (document.getElementById("message").value
            ? " " : "") + word;
        document.getElementById("message").focus();
      };

      row.append(info, button);
      list.append(row);
    });

  if (!list.children.length) {
    list.textContent = "No words found yet 🌙";
  }
}

function addWord() {
  const word = document.getElementById("newWord")
    .value.trim().toLowerCase();
  const meaning = document.getElementById("meaning")
    .value.trim();
  const notice = document.getElementById("notice");

  if (!/^[a-z][a-z'-]*$/.test(word) || !meaning) {
    notice.textContent =
      "Use a word with letters and enter its meaning.";
    return;
  }

  if (Object.hasOwn(words, word)) {
    notice.textContent =
      "That word already exists. Try another! 🌸";
    return;
  }

  words[word] = meaning;
  document.getElementById("newWord").value = "";
  document.getElementById("meaning").value = "";
  notice.textContent = "Word added! ✨";
  renderWords();
}

renderWords();
</script>
</body>
</html>


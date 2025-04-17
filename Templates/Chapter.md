<%*
const title = tp.file.title; // e.g. "Mark 15"
const match = title.match(/^(.+?) (\d+)$/);

if (!match) {
  tR += "---\nerror: could not parse book and chapter\n---\n";
  return;
}

const book = match[1];              // "Mark"
const chapter = match[2];           // "15" as string
const chapterNumber = parseInt(chapter);
const prev = chapterNumber - 1;
const next = chapterNumber + 1;

const isOT = [
  "Genesis","Exodus","Leviticus","Numbers","Deuteronomy","Joshua","Judges","Ruth","1 Samuel","2 Samuel",
  "1 Kings","2 Kings","1 Chronicles","2 Chronicles","Ezra","Nehemiah","Esther","Job","Psalms","Proverbs",
  "Ecclesiastes","Song of Solomon","Isaiah","Jeremiah","Lamentations","Ezekiel","Daniel","Hosea","Joel",
  "Amos","Obadiah","Jonah","Micah","Nahum","Habakkuk","Zephaniah","Haggai","Zechariah","Malachi"
].includes(book);

const tag = isOT ? "bible/ot" : "bible/nt";
const chapterPrefix = `${book} ${chapter}.`; // e.g. "Mark 15."

// --- YAML FRONTMATTER ---
tR += "---\n";
tR += `book: ${book}\n`;
tR += `chapter: ${chapterNumber}\n`;
tR += `reference: ${book} ${chapter}\n`;
tR += `translation: WEB\n`;
tR += `tags: [bible/chapter, ${tag}]\n`;
tR += "---\n\n";

// --- NAVIGATION (TOP) ---
if (prev > 0) {
  tR += `[[${book} ${prev}|<-]] `;
}
tR += `✞ `;
tR += `[[${book} ${next}|->]]\n\n`;

// --- TITLE ---
tR += `# ${book} ${chapter}\n\n`;

// --- EMBED ALL MATCHING VERSES ---
const verseFiles = app.vault.getMarkdownFiles()
  .filter(f => f.basename.startsWith(chapterPrefix))
  .sort((a, b) => {
    const aVerse = parseFloat(a.basename.split('.').pop());
    const bVerse = parseFloat(b.basename.split('.').pop());
    return aVerse - bVerse;
  });

verseFiles.forEach(f => {
  tR += `![[${f.basename}]]\n\n`;
});

// --- NAVIGATION (BOTTOM) ---
if (prev > 0) {
  tR += `[[${book} ${prev}|<-]] `;
}
tR += `✞ `;
tR += `[[${book} ${next}|->]]\n`;
%>
/**
 * Static Site Generator (ssg.ts)
 *
 * - Reads markdown files in /content, applies simple templates, writes to /dist
 * - npm install marked
 *
 * Usage:
 *  ts-node src/ssg.ts build
 */
import fs from 'fs/promises';
import path from 'path';
import { marked } from 'marked';

const CONTENT = path.join(__dirname, '../content');
const DIST = path.join(__dirname, '../dist');

async function build() {
  await fs.mkdir(DIST, { recursive: true });
  const files = await fs.readdir(CONTENT);
  for (const f of files.filter(x => x.endsWith('.md'))) {
    const md = await fs.readFile(path.join(CONTENT, f), 'utf-8');
    const html = marked(md);
    const out = `<!doctype html><html><head><meta charset="utf-8"><title>${f}</title></head><body>${html}</body></html>`;
    await fs.writeFile(path.join(DIST, f.replace('.md', '.html')), out);
    console.log('Built', f);
  }
}

if (process.argv[2] === 'build') build();

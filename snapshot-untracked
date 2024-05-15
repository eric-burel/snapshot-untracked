#! /usr/bin/env bun
// bun doesn't yet have a readdir function
import { $ } from "bun"
import { dirname } from "path"
// In a monorepo, config files should be at most at depth 4
// We ignore notoriously big folders
const untrackedFiles = $`find . -type f \
-maxdepth 4 \
-not -path './.git/*' \
-not -path './node_modules/*' \
-not -path './static/*' \
-not -path './public/*' \
-not -path './.cache/*' \
| git check-ignore --stdin`.lines()

let fileCounts = new Map()
let ignoredDirs = new Set()
let files: Array<string> = []
for await (let file of untrackedFiles) {
    if (!file) continue
    const dir = dirname(file)
    if (ignoredDirs.has(dir)) { continue }
    if (!fileCounts.has(dir)) {
        fileCounts.set(dir, parseInt((await $`ls ${dir} | wc -l`).text()))
    }
    // if parent dir has many untracked files, it's most probably a generated directory
    let count = fileCounts.get(dir)
    if (count > 20) {
        console.info(`Ignoring directory ${dir}`)
        ignoredDirs.add(dir)
    }
    files.push(file)
}
console.log(files)



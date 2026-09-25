# Wordy (Word Connect)

**An Android word puzzle game made with libGDX (Java).** Swipe across a wheel of letters to spell words and fill a crossword grid. It has 10,034 English and 10,032 Turkish levels.

This game is built on a purchased CodeCanyon template, **"Word Connect Android Game"**. The template's manual is included as `docs/template-help.pdf`. This repository is the renamed, configured and maintained version that was published on Google Play (app name *Wordy*). [Template and customization](#template-and-customization) separates what the template provides from what was changed.

---

## Gameplay

- **Core loop:** drag across the letters on the dial to spell a word. Words that belong to the board fill in their row or column; complete every word to clear the level.
- **Levels:** 10,034 in English and 10,032 in Turkish. The first levels use 3–4 letters and 2–4 words. From level 1,000 on they use 7–8 letters and 9–16 words, on grids up to 12 × 10.
- **Hints:**
  - Shuffle.
  - Reveal a random tile, or a tile you choose.
  - Reveal several random tiles at once.
  - **Rocket:** reveals a word's first and last letters and puts collectible coins on the rest.
  - Players start with two of each hint and can win more. When none are left, hints cost coins.
- **Coins:** come from combos (several correct words in a row), bonus words, boosters, rewarded videos and push-message gifts.
- **Bonus words:** valid words that are not on the board are collected; every 50 pays out coins. A word filter (`vulgar.txt`) keeps offensive words from counting.
- **Boosters** (from level 15, every 5th level, in rotation):
  - a UFO to tap before it flies off;
  - a bomb to defuse before you run out of moves (a video buys 5 more);
  - a gold pack;
  - "Jumpy" the monster, who drops coins when caught.
- **Extras:**
  - A lucky wheel once every 24 hours.
  - An in-game dictionary: tap a solved word to see its definition (English only).
  - Interactive tutorials for the dial, shuffle and every hint.
  - Seasonal title screens (Halloween, Thanksgiving, New Year) and 10 game backgrounds that rotate every 10 levels.
  - A rate-us prompt and a support e-mail button.
- **Languages:** the player picks English or Turkish at first launch and can switch in the menu. Level progress is kept separately for each language.

**Status:** Published on Google Play. The first release build dates from December 2020. The project was updated in 2022 for Google Play's target-API and SDK requirements, and the latest release bundle was built in January 2023.

**Google Play:** https://play.google.com/store/apps/details?id=com.wordfind.mchunter. The store listing uses the application ID `com.wordfind.mchunter`; this source snapshot is configured for `com.tblood.wordfind`.

Known issues in this snapshot:

- `android/assets/data/en/strings.properties` has a Turkish text for `spin_again`. This label doesn't appear with the current one-spin-a-day wheel setting.

## Template and customization

The project started from **"Word Connect Android Game"** (CodeCanyon; manual in `docs/template-help.pdf`). The template supplies:

- the complete libGDX game: `core`, the Android and desktop launchers, and the ads, push and billing integration;
- all art, sounds and fonts, including the PSD sources and sprite-sheet projects in `resources/`;
- the English and Turkish word lists and their ~10,000 levels each. The manual describes both languages as part of the template, and the level files date from October 2020;
- the level generator in `resources/CrosswordGenerator`.

**Customized and published:**

- **App identity:** app name **Wordy** (`android/res/values/strings.xml`) and application ID `com.tblood.wordfind` (`android/build.gradle`) instead of the template defaults. The Java package stays `word.game`.
- **AdMob:** the project's AdMob app and ad-unit IDs in `strings.xml`, with test ads off. Interstitial and rewarded ads are enabled.
- **Monetization:** ads only. In-app purchases are switched off (`IAP_ENABLED` is `false`), so the shop and Play Billing code are inactive.
- **Firebase:** a Firebase configuration for `com.tblood.wordfind`, used for push messages. `android/google-services.json` is not in the repo.
- **Settings and text:** `GameConfig.java`, `UIConfig.java` and both `strings.properties` files were edited in February 2022. Several `GameConfig` values differ from the template's inline comments, e.g. 500 starting coins where the comment says 0.
- **2022–2023 updates for Google Play requirements:**
  - Compile/target SDK 31, with `android:exported` set in the manifest.
  - Android Gradle Plugin 7.1.0 and Gradle 7.2.
  - Ads code on the Mobile Ads SDK 20 API (`InterstitialAd.load`, `RewardedAd.load`) and billing on Play Billing Library 4.0 (`queryPurchasesAsync`).
  - New release builds.
  - The `//UPDATE//` markers in `IAPActivity.java` suggest that part of this code came from a template update.
- **Published** on Google Play; first release build December 2020.

_Owner: add any other changes you made here._

## Tech stack

| Area | What it uses |
|---|---|
| Engine | **libGDX 1.9.11** (Java), scene2d UI, custom GLSL shaders |
| Modules | `core` (shared game code), `android` (launcher and platform services), `desktop` (LWJGL2 launcher for testing on PC) |
| App | Application ID `com.tblood.wordfind`, versionName 1.3.3 (versionCode 1), code package `word.game` |
| Android build | Android Gradle Plugin 7.1.0, Gradle 7.2 wrapper; compileSdk/targetSdk 31, minSdk 16; multidex; R8 shrinking for release (`proguard-rules.pro`) |
| Ads and consent | Google Mobile Ads SDK 20.5.0 (AdMob interstitial + rewarded), User Messaging Platform 2.0.0 |
| In-app purchases | Google Play Billing Library 4.0.0 (integrated, switched off) |
| Push messages | Firebase Cloud Messaging (Firebase BoM 25.11.0, google-services plugin 4.3.10) |
| Dictionary lookups | jsoup 1.13.1 (parses Princeton WordNet's HTML results) |
| Art pipeline | 4 texture atlases exported at 3 scales (`_sd`, `_hd`, `_hdr`) from a gdx-texture-packer-gui project (`resources/word_connect.tpproj`); Hiero bitmap fonts made from Montserrat; PSD sources |
| Level tooling | `resources/CrosswordGenerator`: standalone Java program with an SQLite word database (`resources/db/word_source.db`; sqlite-jdbc 3.30.1, org.json 20190722) |

> The build script also carries libGDX-setup leftovers that the game doesn't use: the Box2D dependency and its native libraries, the GWT plugin and a RoboVM version.

## What's in the codebase

All of this code comes from the template; files changed later are listed [above](#template-and-customization).

Game code lives in **`core/src/word/game/`**: 126 Java files, about 22k lines. There are also 9 Android classes, 6 desktop classes and 16 generator classes, so 157 Java files in total.

| Package | Contents |
|---|---|
| `screens` | `SplashScreen` (asset loading, first-run language choice), `IntroScreen` (title screen), `GameScreen` (about 2.3k lines, the level screen), shared `BaseScreen` |
| `controllers` | `GameController`: answer checking, bonus words, combos, booster triggers, level end |
| `model` | `GameData` (level loading and save data), `BoardModel`, `CellModel`, `Level`, `Word`, `Constants` |
| `ui/board` | Crossword grid (`BoardView`, `CellView`) and the board boosters (`Ufo`, `Bomb`, `GoldPack`, `Monster`) |
| `ui/dial` | Letter dial (`Dial`, `DialButton`), connection lines, combo ring animation and particles |
| `ui/hint` | Hint buttons, `Rocket`, idle-hint timer, rewarded-video button |
| `ui/dialogs` | Dictionary, how to play, bomb, watch-and-earn, remove ads, bonus words, menu and language (`menu/`), lucky wheel (`wheel/`), shop (`iap/`) |
| `ui/tutorial` | Interactive tutorials for the dial, hint buttons and boosters |
| `ui` (other) | Level-end view, combo display, feedback ribbons, toasts, tooltips; `top_panel` (coins, level, combo), `preview` (word being spelled), `confetti`, `calendar` (date interface for seasonal content) |
| `pool` | Object pools for letters, cells, dial buttons, coins, words and particles |
| `managers` | `ResourceManager` (asset loading, resolution choice), `LanguageManager` (UI text via `I18NBundle`), `HintManager` (coins and hint counts), `AdManager` interface, `ConnectionManager` |
| `config` | `GameConfig` (economy, ad frequency, boosters, languages, IAP catalog), `UIConfig` (colors, layout, backgrounds), `SoundConfig` |
| `actions`, `graphics`, `util`, `net`, `i18n`, `events` | Bezier/curve scene2d actions, atlas regions and nine-patches, shader actors, screen shake and UI helpers, network/dictionary interfaces, locale model, dictionary event |

Outside `core`:

- **`android/src/word/game/`**: the activity chain (`AndroidLauncher`, `IAPActivity`, `AdActivity`), the Firebase messaging service, the English WordNet lookup, and date and network helpers.
- **`android/assets/data/<lang>/`** (`en`, `tr`):
  - `levels/`: one JSON file per level.
  - `words.txt`: every allowed word as `id:WORD` pairs, about 32.5k English and 31.1k Turkish.
  - `vulgar.txt`: the word filter.
  - `strings.properties`: UI text.
- **`android/assets/`** (other folders): texture atlases, bitmap fonts, sound effects, shaders, title and game backgrounds.
- **`desktop/`**: LWJGL2 launcher (366 × 650 window). No ads, billing or push.
- **`resources/`**: the template's source assets:
  - PSDs and the sprite-sheet folders `atlas_1` to `atlas_4`;
  - the TexturePacker project;
  - Hiero font projects and TTFs;
  - the SQLite word database;
  - the level generator.

### Code highlights

- **`core/src/word/game/model/GameData.java`:** loads and saves game state.
  - Each level is a tiny JSON file. For example, `"o": "9,9,KINFOLK"` gives the grid size and dial letters, and `"a"`/`"d"` list the across/down words as `wordId,x,y`.
  - Word IDs are looked up in `words.txt`.
  - Progress (solved words, rocket words, tile states packed as `(x << 8) | y`) is saved per language in libGDX `Preferences`.
- **`core/src/word/game/controllers/GameController.java`:** checks each swiped word against the board first, then against the bonus-word list and the word filter.
  - Found words go through an animation queue.
  - Crossing words completed as a side effect are solved automatically.
  - Boosters react to the tiles that get filled.
- **`core/src/word/game/ui/dial/Dial.java`** + **`graphics/shader/LineShader.java`:** the line between selected letters is drawn by a fragment shader (`android/assets/shader/line.frag`: anti-aliased distance to a line segment), not sprites. `DialAnimationContainer` uses `dial.fsh` for a rainbow ring around the dial that grows with the combo count.
- **`core/src/word/game/managers/ResourceManager.java`:** picks the `_sd`, `_hd` or `_hdr` asset set for the screen size and computes a global scale factor, so one set of art serves small and high-resolution phones.
- **`android/src/word/game/activity/`:** `AndroidLauncher` extends `IAPActivity`, which extends `AdActivity`. The core only talks to interfaces (`AdManager`, `ShoppingProcessor`, `AppExit`, `RateUsLauncher`, `SupportRequest`, `WordMeaningProvider`, `Network`, `DateUtil`). `core` has no Android dependencies and also runs through the desktop launcher.
- **`android/src/word/game/WordConnectFirebaseMessagingService.java`:** a push message to the topic `coin_topic` with a `coins` value is passed on by local broadcast to `WordConnectGame.notificationReceived()`, which adds the coins and shows a dialog.

### Level generator (`resources/CrosswordGenerator`)

A standalone Java program that comes with the template. It produced the level files in `android/assets/data/<lang>/levels/`. For each level index it:

1. Reads that level's rules from `src/generator/level_definitions/LevelDefinitions_<lang>.java`:
   - dial length;
   - word difficulty score 1–4;
   - minimum word length;
   - min/max number of words;
   - how many levels must pass before a word may reappear.
2. Picks a random word with that length and score from the `words_<lang>` table in `resources/db/word_source.db`. Its letters become the dial.
3. Collects every database word that can be spelled from those letters.
4. Lays the words out as a crossword of at most 10 rows × 12 columns. The layout code is based on the MIT-licensed [satchamo/Crossword-Generator](https://github.com/satchamo/Crossword-Generator), credited in the template manual.
5. Discards boards with too few words and duplicates of existing levels.
6. Moves the used words' `reappear` level forward so words spread across the level set.
7. Writes the level as JSON, and exports `words.txt` (all words with score 0 or higher) and `vulgar.txt` (score -1).

Word scores in the database: -1 offensive (never used, filtered), 0 obscure (bonus word only), 1–4 easy to hard. The database and output paths in `src/generator/Main.java` still point to the template author's machine.

## Integrated services and third-party content

| Service / content | Used for |
|---|---|
| Google AdMob (Mobile Ads SDK 20.5.0) | Interstitials at the start of a level. There are none during the tutorial levels, then they come more often as levels go up (`GameConfig.shouldWeShowAnInterstitialAdForThisLevel`). Rewarded videos give 20 coins or 5 extra bomb moves, at most one every 40 s. IDs and switches are in `android/res/values/strings.xml`. |
| Google User Messaging Platform 2.0.0 | GDPR consent form code in `AdActivity` (not called at the moment; see Status) |
| Firebase Cloud Messaging | Push messages to the `coin_topic` topic; a `coins` data value gives coins in game |
| Google Play Billing Library 4.0.0 | Shop with 6 coin packs, 4 bundles (coins + hints) and Remove Ads; inactive because `IAP_ENABLED` is `false` |
| Princeton WordNet (web search) | English definitions for the dictionary dialog, fetched over HTTP (hence `usesCleartextTraffic`) and parsed with jsoup. There is no Turkish dictionary. |
| Open-source libraries | libGDX (Apache 2.0), jsoup (MIT); for the generator, sqlite-jdbc (Apache 2.0) and org.json (JSON License) |
| Template media | Montserrat fonts plus icons, sounds and backgrounds from iconfinder, flaticon, freesound, pngtree, pexels and freepik; licenses and required attributions are listed at the end of `docs/template-help.pdf` |

## About this repository

This public repository is a **showcase**. It contains the documentation for this project. The game is built on a licensed CodeCanyon template, so its source code is kept in a private repository.

Copyright © Omer Avcioglu (McHunter Studio). **All rights reserved.** Viewing only; see LICENSE.

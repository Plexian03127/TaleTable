# Tale Table – 프롬프트 문서

이 문서는 두 부분으로 되어 있습니다.

1. **개발 프롬프트 기록**: 게임을 만들면서 AI(Claude)에게 입력한 주요 프롬프트와 그 결과
2. **게임 내 AI 프롬프트**: 실제 게임 코드가 플레이 중 AI에게 보내는 프롬프트 원문

---

## 1. 개발 프롬프트 기록

개발은 Claude와의 대화로 진행했습니다. 아래는 주요 단계별 프롬프트입니다(개발자 입력 원문).

| # | 단계 | 입력한 프롬프트 | 결과 |
|---|---|---|---|
| 1 | 기획 | AI를 활용한 TRPG 게임을 만들고 싶어 | 설계 방향 제안: AI는 서술, 코드는 규칙 계산(역할 분리), 상태 JSON 관리, MVP 범위 |
| 2 | 형태 결정 | 혼자 즐기는 웹 게임으로 | 첫 프로토타입: d20 판정, 공격·탐색·도주, AI 장면 서술 |
| 3 | 캐릭터 | 먼저 직업과 능력치 선택 | 직업 3종, 능력치(힘·민첩·지혜) 추가 포인트 배분 |
| 4 | 전투 확장 | 여러 장면과 적 | 장면 3개, 적 3종, 마지막 장면 보스 |
| 5 | 세계관 | (세계관 변경 요청) | 세계관 3종: 어둠의 던전 / 네온 도시 / 폐우주정거장 |
| 6 | 모험 요소 | 적과 싸우는 것 외 모험하는 스토리도 넣고 싶어. NPC와 대화를 하거나 퀘스트를 받거나 여러 지역이나 장소를 이동하거나 | 장소 이동, NPC 대화, 의뢰(퀘스트) 시스템, 자연어 행동 분류 |
| 7 | 의뢰 조정 | 퀘스트는 무조건 나오게 하지 말고 확률적으로 나오거나 스토리 진행에 필요할 경우에만 나오도록 바꿔줘 | 의뢰 확률 등장, 대화와 수락 분리 |
| 8 | 분기·엔딩 | 의뢰 분기와 엔딩 확장으로 이어서 설계해 줘 | 분기 3곳(의뢰·조력자·최종 적), 엔딩 6종, 엔딩 수집 → 이후 '맺어진 인연' 추가로 7종 |
| 9 | 아트 | 혹시 각 세계관의 이미지를 생성해서 추가해 줄 수 있어? | SVG 벡터 그림으로 임시 일러스트 |
| 10 | 아트 프롬프트 | 각 세계관 이미지를 이미지 생성 AI로 만들건데 각 세계관에 대한 디테일 설명을 프롬프트로 작성해줘 | 이미지 생성 AI용 영문 프롬프트 + 부정 프롬프트 → 수채화풍 일러스트 적용 |
| 11 | 배포 | 독립 실행형 웹 페이지로 플레이할 때 AI 연결 방법 / 혹시 다른 사람이 플레이할 때 본인이 사용하는 클라우드 AI(클로드, 챗지피티, 제미나이 등)를 연결하는 방법이 있을까? | 아티팩트 링크(플레이어 Claude 계정) + BYOK(Claude·Gemini·OpenRouter API 키) 연결 |
| 12 | 일러스트 | 혹시 게임 중 상황에 맞는 일러스트를 자동으로 생성해서 화면에 띄우게 하는 건 어려울까? → B방법이 좋겠네. 그럼 NPC와 적 일러스트 프롬프트를 짜줘. 대화 내용 사이에 들어갈 수 있도록 비율은 가로로 | 미리 만든 그림을 상황에 맞게 표시하는 방식 채택, NPC 15·적 9·엔딩 21종 일러스트 프롬프트 작성 |
| 13 | 저장 | 타이틀에서 이어하기를 누르면 이어서 할 거냐는 창을 띄우고 이어하기 버튼과 저장된 모험 삭제하기 버튼을 추가해줘 | 3턴 자동 저장, 이어하기 확인 창, 저장 삭제 |
| 14 | UX | 저장하고 나가기 버튼을 누를 때 경고창이 뜨도록 해줘 | 화면 내 확인 대화상자, 타이틀 복귀 버튼 |

### 이미지 생성 프롬프트 예시 (세계관: 어둠의 던전)

```text
Cinematic wide establishing shot of the ruined entrance to an ancient underground cathedral, dark fantasy.
A massive collapsed gothic pointed arch of black weathered stone dominates the center, its inner doorway
glowing with faint warm golden light. Broken stone pillars frame both sides. Thick low-lying pale fog rolls
across the ground, and a huge full moon hangs in a deep navy starry sky, casting cold silver rim light on the
stone. Inside the dark doorway, two small glowing amber eyes of an unseen shadow wolf watch from the darkness.
Carved on the arch are faint glowing golden runes in an invented fictional script, abstract and unreadable.
```

- 공통 부정 프롬프트: `text, letters, readable writing, watermark, logo, signature, UI, frame, border, blurry, low quality, deformed`
- 공통 화풍: 잉크 선화 + 수채 번짐(ink linework with soft watercolor washes), 16:9 가로

---

## 2. 게임 내 AI 프롬프트

게임은 한 턴에 AI를 최대 2번 호출합니다. ① 행동 분류 → (코드가 판정) → ② 결과 서술. 게임 시작 시에는 도입 서술을 1번 호출합니다.
`${...}` 부분은 코드가 현재 게임 상태로 채워 넣는 변수입니다. 모든 응답은 **JSON**으로 받아 코드가 처리합니다.

### 2-0. 시스템 프롬프트 (API 키 연결 시)

```text
You are the game master engine of a Korean TRPG. Follow the instructions exactly and reply with a single valid JSON object only, no markdown.
```

### 2-1. 도입 장면 서술 (게임 시작 시 1회)

```text
당신은 ${W.tone} 분위기 TRPG의 게임 마스터입니다. 아래 프롤로그를 이어, 플레이어가 본격적으로 모험을 시작하는 도입 장면을 한국어 3~4문장으로 생생히 묘사하세요. 게임 수치와 규칙은 언급하지 마세요.
프롤로그: ${W.pro.join(" ")}
플레이어: ${S.cls.n} / 시작 장소: ${L[0].n} – ${L[0].d} / 등장 인물(이름은 아직 알려지지 않았으니 직업으로만 지칭): ${npcsAt(L[0]).map(x=>shown(x.n)).join(", ")}
JSON으로만 답하세요: {"narration":"..."}
```

### 2-2. 행동 분류기 (매 턴)

플레이어의 자유 입력을 게임 규칙이 이해할 수 있는 형태로 바꿉니다. AI가 실패하면 키워드 규칙으로 대신 분류합니다.

```text
TRPG 행동 분류기입니다. 플레이어 행동: “${txt}” / 직업: ${S.cls.n}
현재 장소: ${loc0.n} / 이동 가능: ${loc0.x.map(i=>L[i].n).join(", ")} / NPC: ${npcsAt(loc0).map(x=>x.n).join(", ")||"없음"} / 적: ${S.eh>0?S.en:"없음"}
소지품: ${S.items.join(", ")||"없음"}
type: attack(적에게 피해를 주려는 행동), search(주변 탐색·아이템 찾기), flee(도망), item(소지품 사용·장착), move(다른 장소로 이동), talk(NPC와 대화), other(설득·도발·방어·지형 이용 등 그 외)
item: type이 item일 때 소지품 목록의 정확한 이름, 아니면 ""
dest: type이 move일 때 이동 가능 장소의 정확한 이름, 아니면 ""
intent: type이 talk일 때 의도 — 의뢰인에게: accept(수락)|decline(거절)|haggle(보상 흥정)|honest(정직한 보고)|lie(거짓 보고)|greed(보상 독차지); 조력자에게: ask(정중한 부탁)|persuade(거래·설득)|steal(훔치기); 전투 중 최종 적을 설득하려는 행동은 type을 talk로 두고 persuade; 해당 없으면 none
npc: type이 talk일 때 대화 상대 NPC의 정확한 이름, 아니면 ""
stat: 행동에 가장 어울리는 능력치(힘/민첩/지혜 중 하나)
difficulty: easy|normal|hard (상황에 비해 쉬우면 easy, 비현실적이거나 무모하면 hard)
JSON만 답하세요: {"type":"","stat":"","difficulty":"","item":"","dest":"","intent":"none","npc":""}
```

### 2-3. GM 결과 서술 (매 턴)

코드가 계산한 판정 결과를 **바꾸지 못하게** 규칙으로 고정하고, NPC 성격·말투·숨은 사정과 최근 이야기 3개를 함께 전달합니다.

```text
당신은 ${W.tone} 분위기 TRPG의 게임 마스터입니다. 한국어로 2~3문장, 생생하게 묘사하세요.
규칙: 아래 판정 결과와 수치를 절대 바꾸지 말고, 새 피해나 아이템을 만들지 마세요. 플레이어가 원한 행동을 그대로 묘사하되 결과는 판정에 따르세요. NPC와 대화하는 경우 NPC 설정의 [성격]과 [말투]를 일관되게 지켜 대사를 큰따옴표로 포함하고(NPC 설정의 [숨은 사정]은 아래 공개 단계 지시를 반드시 따르고, 지시가 없으면 언급하지 마세요), 플레이어가 질문했다면 그 질문에 직접 답하세요(세계관 안에서 소문과 설정을 자유롭게 지어내도 되지만 게임 수치, 아이템, 보상, 의뢰 내용은 바꾸지 마세요). 대화는 3~4문장까지 허용합니다. 이동한 경우 새 장소의 분위기를 묘사하세요. 숫자는 언급하지 않아도 됩니다.
플레이어 직업: ${S.cls.n}. 현재 장소: ${lc.n} – ${lc.d}. ${S.eh>0?"적: "+S.en+" (HP "+S.eh+"/"+S.emh+")":"적 없음"}. 플레이어 HP ${S.hp}/${S.mhp}.
${npcCtx}${S.qAvail?"의뢰 내용: "+W.q+".":""}
플레이어 행동: “${txt}”
결과: ${noRoll?"행동 결과":ok?"성공":"실패"} – ${res}
${fin?"엔딩 장면입니다. "+epi(fin)+" 엔딩: "+ENDS[fin].t+" – "+ENDS[fin].d+(fin==="bond"?" 이들이 배웅하거나 각자의 사연이 이어지는 장면을 포함하세요.":""):ev==="clear"?"적을 쓰러뜨려 전투가 끝났습니다.":""}
최근 이야기: ${S.hist.slice(-3).join(" / ")}
${tgN?'JSON으로만 답하세요(NPC 대화 형식): {"before":"대사 전 지문·묘사 1~2문장","line":"NPC의 대사만, 큰따옴표 없이","after":"대사 후 지문·묘사 1~2문장(엔딩이면 후일담 포함, 이 경우 분량은 엔딩 지시를 따르세요)"} — 지문에는 NPC의 대사를 넣지 마세요.':'JSON으로만 답하세요: {"narration":"..."}'}
```

### 프롬프트 설계 포인트

- **결과 고정**: "판정 결과와 수치를 절대 바꾸지 말고, 새 피해나 아이템을 만들지 마세요" — AI의 환각이 게임 규칙을 깨지 않도록 함
- **JSON 출력 강제**: 서술과 데이터를 분리해 코드가 안정적으로 파싱
- **NPC 일관성**: [성격]·[말투]·[숨은 사정]을 매번 전달, 숨은 사정은 신뢰도 단계에 따라 공개 지시
- **기억 유지**: 최근 이야기 3턴 요약을 함께 보내 맥락 유지
- **엔딩**: 엔딩 판정은 코드가 하고, AI는 해당 엔딩의 후일담만 묘사

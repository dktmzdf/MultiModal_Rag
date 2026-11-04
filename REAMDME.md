git clone <저장소 주소>
cd multimodal-rag

# 가상환경 생성 및 활성화
python -m venv .venv
source .venv/bin/activate          # 윈도우: .venv\Scripts\activate

# 의존성 설치
pip install --upgrade pip
pip install -r requirements.txt     # 또는 requirements.lock.txt

# 커널 등록
python -m ipykernel install --user --name multimodal-rag

# .env 작성
cp .env.example .env                # 윈도우: copy .env.example .env
# .env 파일을 열어 실제 API 키를 입력
## 주제 4~7: 멀티모달 RAG 실행

프로젝트 루트에서 `.env`에 `OPENAI_API_KEY`를 설정하고 Poppler, Tesseract를 설치한 뒤 실행해. `hi_res` PDF 분석은 모델 파일 다운로드와 추가 시스템 의존성이 필요할 수 있어.

```bash
.venv/bin/python src/multimodal_rag.py data/과기부.pdf "문서의 주요 내용을 설명해줘" --compare
```

PDF의 텍스트와 표를 추출해 요약하고 이미지도 요약해. 요약을 Chroma에 색인하고, 검색 결과에 연결된 원본 텍스트·표·이미지를 모델에 전달해 답변을 생성해. `--compare`는 같은 질문으로 텍스트 전용 답변도 출력해. 추출 이미지는 실행할 때마다 `data/figures/` 아래 새 폴더에 저장돼. 검색 인덱스와 원본 저장소는 메모리에만 남아서 다시 실행하면 추출과 요약을 다시 해.

기본 이미지 요약은 OpenAI를 사용해. 로컬 Ollama와 `llava:7b`가 준비됐다면 `--llava`로 이미지 요약에 사용할 수 있어. `--translate`는 Ollama의 `llama3.1:8b`로 최종 답변을 한국어로 번역해. 기본 답변도 한국어라서 필요한 경우에만 사용해. OpenAI 요약·임베딩·답변 요청에는 비용이 발생할 수 있어.

```bash
.venv/bin/python -m unittest discover -s tests -v
```


# 폴더 구조
multimodal-rag/
├── .venv/                      # 가상환경 (Git 제외)
├── .env                        # API 키 (Git 제외, 절대 커밋 금지)
├── .env.example                # 키 없는 템플릿 (Git 포함)
├── .gitignore
├── requirements.txt            # 의존성 목록
├── requirements.lock.txt       # 버전 고정 파일
├── README.md                   # 포트폴리오의 얼굴
├── data/
│   ├── invest.pdf              # 원본 PDF
│   └── figures/                # 추출된 이미지 (Git 제외, 용량 큼)
├── notebooks/
│   └── Multi_modal_RAG.ipynb   # 실습 노트북
└── src/
    ├── extract.py              # PDF 파싱 함수
    ├── summarize.py            # 요약 체인
    └── retriever.py            # 다중 벡터 검색기
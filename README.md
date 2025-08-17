## GrabNavi - AI-powered orchestration system for last-mile logistics

# Files
- Synapse/GrabNavi.ipynb: Main notebook with full implementation.
- docs/DESIGN.md: Agent architecture and design.
- docs/PROMPTS.md: Prompt engineering strategies.
- docs/grab.pptx: Detailed Walkthrough ppt
- README.md: Quickstart guide.

# Folder Structure
```
GrabNavi/
│── docs/
│   ├── DESIGN.md                # Agent design & architecture
│   ├── PROMPTS.md               # Prompt and its analysis
│   ├── grab.pptx                # Detailed ppt           
│   └── image.png                # Flow diagram
│── GrabNavi.ipynb               # Main orchestrator agent
└── README.md
```

# Setup
Install dependencies inside Colab or local environment:
bash
!pip install google-generativeai langchain langchain-google-genai networkx pandas pillow


# Run
- Open GrabNavi.ipynb in Jupyter/Colab.
- Execute cells sequentially to run the system.

# Features
- LLM-powered case detection with Gemini.
- Compound case handling (multiple issues in one report).
- NetworkX graph simulation for traffic rerouting.
- Human-in-loop escalation support.


# Notes 
- Uses Gemini-1.5-flash API in Google AI Studio.
- For image-based damage cases, upload the relevant image when prompted.
- We have upload an image already with "download1.jpg" - you can try it when running it

# Open the Colab Notebook: 
[GrabNavi Colab](https://colab.research.google.com/drive/1s5RgnjcO1Feup_5DhT8BFmP3iK8n4-75?usp=sharing)  

# Flow Diagram
![GrabNavi Flow](doc/image.png)

# Voice AI Agent (n8n + Telegram)

A Telegram assistant built in n8n that understands both **voice notes** and **text messages**, answers using an AI Agent, and replies with **text and audio**.

## What it does

1. You send a text message or a voice note to a Telegram bot.
2. The workflow detects which one it is and converts voice to text.
3. An AI Agent (with memory and tools) writes the answer.
4. The bot replies with the answer as text, and also as a spoken audio message.

## Workflow overview

```
Telegram Trigger
      |
    Switch ----- Voice ----> Get a file -> STT -> audio_var ---+
      |                                                        |
      +--------- Text -----> Input_var ------------------------+
                                                               v
                                                           AI Agent
                                              (Chat Model, Memory, Tools)
                                                    |            |
                                                    v            v
                                          Telegram (text)   Basic LLM Chain
                                                                 |
                                                                 v
                                                           TTS -> Send audio
```

## Nodes

| Node | Purpose |
|------|---------|
| Telegram Trigger | Receives every message sent to the bot |
| Switch | Routes the message to the Voice path or the Text path |
| Get a file | Downloads the voice note from Telegram |
| STT (OpenAI Transcribe a Recording) | Converts the voice note to text |
| audio_var / Input_var (Edit Fields) | Put the text into one common field, `chatInput` |
| AI Agent | Understands the request and decides when to use tools |
| OpenAI Chat Model (gpt-4o-mini) | The language model used by the agent |
| Simple Memory | Remembers recent messages, one memory per Telegram chat |
| Tool: Google search (SerpApi) | Looks up current information |
| Tool: Gmail (send message) | Sends an email when asked |
| Tool: Calculator | Does maths |
| Telegram (Send a text message) | Sends the text answer |
| Basic LLM Chain | Rewrites the answer so it sounds natural when spoken |
| TTS (OpenAI Generate Audio) | Converts the answer to audio |
| Telegram (Send an audio file) | Sends the audio reply |

## Languages

Built for **English and Tamil**. The agent replies in the same language the user used.

Known limitation: with automatic language detection, short or unclear Tamil voice notes can be transcribed wrongly. Speaking a full, clear sentence helps.

## Setup

1. Create a Telegram bot with @BotFather and copy its token.
2. Import `My_Voice_AI_Agent.json` in n8n (Workflows > Import from file).
3. Create your own credentials in n8n for: Telegram, OpenAI, SerpApi and Gmail (OAuth2).
4. Select your credentials on each node that shows a warning.
5. Execute the workflow and send a message to your bot.

## Notes

- No API keys or tokens are stored in the JSON file. Add your own credentials after importing.
- Requires an n8n instance reachable over HTTPS (needed by the Telegram trigger).
- OpenAI API usage (speech to text, chat, text to speech) is paid.

## Built with

n8n, Telegram Bot API, OpenAI, SerpApi, Gmail API

# 🍼 `tts_bottle` 

A bolt-on AI-powered hydration assistant that reminds you to drink with witty spoken remarks - GlaDOS in water bottle form!

[#ForTheLoveOfCode](https://github.blog/open-source/for-the-love-of-code-2025/)

## 🎯 Vision

### The Problem
- Cheap water bottles taste weird
- Good bottles get lost or scratched
- Simply forgetting to drink water

### The Solution
Transform an existing nice bottle (Yeti Rambler) into a smart companion:
- **BLE tracking** - never lose your bottle again
- **Water level monitoring** - never forget to drink
- **Neoprene sleeve** - protects from scratches, hides electronics
- **AI personality** - witty, helpful, but not overly "assistant-like"

### Core Philosophy
- **Have fun** - This should be enjoyable to use
- **Make me drink more water** - Primary utility function
- **Make me laugh** - Personality matters more than perfect AI responses

## 🏗️ Architecture

### Hardware Stack
- **ESP32-S3** - Power efficiency, connectivity, performance balance
- **LIS3DH accelerometer** - Interrupt-driven deep sleep, drink detection, fall detection
- **Load cell + amp** - Fluid level detection (1kg = 1L)
- **1000mAh LiPo** - With over-charge/discharge protection
- **Thin Speaker** - 4 Ohm, 2.5W, 28mm
- **MAX98357 I2S Amplifier Module**
- **Makerverse USB-C LiPo charger**
- **LED ring array** - Volume visualization with LCD diffuser sheet
- **ElevenLabs STT** - Natural voice synthesis

### Software Architecture Options

#### Option 1: Phrase Bank (Offline-First)
- Pre-generate common TTS phrases in the cloud
- Download and store locally on ESP32
- Refresh phrases on schedule (daily)
- Fallback phrases for offline scenarios
- **Pros**: Works offline, predictable battery usage
- **Cons**: Less dynamic, limited conversation depth

#### Option 2: Live ConvAI (Dynamic)
- Real-time conversation via ElevenLabs Conversational AI
- Start new conversations based on client events
- Context-aware responses: "Hey Caleb, why haven't you had water yet? It's 10AM!" **[expect response]**
- **Pros**: Dynamic, contextual, more engaging
- **Cons**: Requires constant connectivity, more complex state management

#### Option 3: Hybrid Approach
- Use phrase bank for basic reminders and offline scenarios
- Live ConvAI for interactive conversations
- Smart switching based on connectivity and battery

### Backend Services Needed

#### Phrase Generation Service
- **ElevenLabs API integration** for TTS generation, STT if needed.
- **LLM API Service** for phrase content generation
- **Personality templates** - different response styles (Valley Girl, Pirate, GlaDOS, etc.)
- **LLM Tools** - time, weather, user patterns, internet search (MCP, WebHook tools)
- **Batch phrase generation** - Generate hundreds of unique phrases per personality

##### LLM Workflow for Phrase Generation
The backend service will use structured prompts to generate complete phrase banks:

**Prompt Structure:**
```
Generate [X] unique phrases for a [PERSONALITY] water bottle AI in the [CATEGORY] category.

Personality: [Detailed personality description with speech patterns, vocabulary, tone]
Category: [Specific event type with severity levels if applicable]
Constraints: [Length limits, content restrictions, brand safety]

Example format:
- "Phrase 1"
- "Phrase 2"
- "Phrase 3"
```

**Batch Generation Process:**
1. **Personality Setup**: Define speech patterns, vocabulary, and tone for each personality
2. **Category Mapping**: Generate phrases for each event type and severity level
3. **Quality Control**: Filter for appropriateness, uniqueness, and personality consistency
4. **TTS Conversion**: Convert all phrases to audio using ElevenLabs
5. **Storage**: Package phrases with metadata for ESP32 consumption

**Phrase Volume Estimates:**
- **Per Personality**: ~100-150 unique phrases
- **Per Category**: 5-15 phrases depending on frequency of use
- **Total Storage**: ~50-100MB per personality (compressed audio)

##### Phrase Categories & Examples
Each personality gets a complete phrase bank across multiple categories:

**Valley Girl Personality:**
- **Level 1 Reminders**: "Hey girl! Better have some water or you'll break out."
- **Level 2 Reminders**: "Umm, like you totally have bags under your eyes. Drink maybe?"
- **Level 3-4 Reminders**: Increasingly aggressive as dehydration worsens
- **Network Issues**: "OMG, like I can't even connect right now. Help?"
- **Low Battery**: "I'm literally dying here. Charge me up!"
- **Magic 8 Ball**: "Not likely, babe." / "Totally, for sure!"
- **Lost Bottle**: "Don't leave me here alone!"
- **Fall Detection**: "Hey, watch it! You almost broke my face!"

**Pirate Personality:**
- **Level 1 Reminders**: "Arr, the seabreeze be parchin' me throat. Fancy a swig, Cap'n?"
- **Level 2 Reminders**: "Ye be lookin' parched as a desert island, matey!"
- **Network Issues**: "The signal be lost at sea, Cap'n!"
- **Low Battery**: "The ship's power be runnin' low, arr!"
- **Magic 8 Ball**: "Aye, the stars align!" / "Nay, the winds be against ye!"
- **Lost Bottle**: "Don't abandon ship, capn!"
- **Fall Detection**: "Avast! Ye nearly sent me to Davy Jones' locker!"

**GlaDOS Personality:**
- **Level 1 Reminders**: "I notice you haven't consumed any water in the last 47 minutes. This is not a test."
- **Level 2 Reminders**: "Your dehydration levels are reaching critical mass. I'm not even being sarcastic."
- **Network Issues**: "I seem to have lost connection to the facility. How... inconvenient."
- **Low Battery**: "My power reserves are depleting. Unlike your water reserves, which are already depleted."
- **Magic 8 Ball**: "The probability of that happening is approximately 3.2%"
- **Lost Bottle**: "I appear to have been misplaced. This is your fault."
- **Fall Detection**: "That was almost a fatal error. Please be more careful."

#### Voice AI Management (if going live route)
- **WebRTC server** with `PipeCat-ESP32` or **WebSockets** with ElevenLabs ConvAI.
- **Conversation state management**
- **User context tracking** (hydration patterns, preferences)
- **Fallback handling** for connectivity issues

#### Configuration Management
- **Static web client** (like Meshtastic) for device configuration over Bluetooth
- **WiFi network management**
- **Voice/personality selection**
- **LED behavior configuration**

## 📋 Implementation Plan

### Phase 1: Web App Prototype ✅ (Ready to start)
- Simple web interface with mock sensor events
- Button mapping: `drank water`, `set water level`, `bottle moved`, etc.
- Basic TTS integration for testing personality
- **Goal**: Validate the concept and user experience

### Phase 2: Hardware Prototype 🔄 (Parts purchased)
- Off-bottle hardware testing
- Basic live TTS functionality
- Sensor integration (accelerometer, load cell)
- **Goal**: Prove hardware feasibility

### Phase 3: Phrase Bank Implementation
- Backend phrase generation service
- ESP32 phrase storage and playback
- Offline functionality
- **Goal**: Reliable offline operation

### Phase 4: External Mounting
- Neoprene sleeve design and fabrication
- Component integration into sleeve
- Battery optimization
- **Goal**: Portable, practical form factor

### Phase 5: UI/UX Optimization
- LED ring visualization
- Sound design and speaker optimization
- User interaction patterns
- **Goal**: Polished user experience

### Phase 6: Advanced Features (Optional)
- ConvAI live conversation implementation
- Wake word detection
- Advanced sensor fusion
- **Goal**: Enhanced interactivity

## 🚧 Current Status

### ✅ Completed
- Project concept and architecture planning
- Parts purchased (ESP32-S3, sensors, speakers, etc.)
- Test phrases generated
- Hardware stack defined

### 🔄 In Progress
- Implementation planning
- Backend architecture decisions

### 📋 Next Steps
1. Start Phase 1: Web app prototype
2. Decide on phrase bank vs. live ConvAI approach
3. Design backend services architecture
4. Begin hardware prototyping

## 🤔 Design Decisions & Considerations

### Network Independence
- **Goal**: Minimize phone dependency
- **Strategy**: Local phrase storage with cloud refresh
- **Fallback**: Pre-generated offline messages

### Battery Life
- **Deep sleep** via accelerometer interrupts
- **Scheduled phrase refresh** to minimize active time
- **Efficient sensor polling** strategies

### Personality Design
- **Inspiration**: GlaDOS, Wall-E robots
- **Traits**: Intelligent, quirky, role-constrained
- **Avoid**: Overly helpful AI assistant behavior

### Hardware Integration
- **Approach**: External neoprene sleeve
- **Reasoning**: Preserves bottle aesthetics, easier maintenance
- **Alternative considered**: Built into cap (too small for current skills)

## 🎯 Success Metrics
- [ ] Reminds me to drink water consistently
- [ ] Makes me laugh at least once per day
- [ ] Battery lasts at least 3 days
- [ ] Not annoyed by the system or reminders
- [ ] Survives daily use without damage

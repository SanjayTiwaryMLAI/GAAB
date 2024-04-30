# Logo addition
1. Go to the "public" folder and open the "index.html" file. Change the title to "<title>GeM Virtual Assistant</title>".
 
2. Replace the favicon in the "public" folder.
(./gaab-ui-change-like-dislike/source/ui-chat/public)
 
3. Create a new folder called "assets" in the "src" folder. Add the GeM SVG logo to this folder.
 
4. Paste the "index.d.ts" file in the "src" folder.
 
(gaab-ui-change-like-dislike/source/ui-chat/src/App.js)
 
5. Open the "app.js" file and add the following line to import the GeM logo: "import logo from './assets/gem-new-logo-v6.svg';"
 
6. Replace the code for the identity object at line number 77 in "app.js" with the following:
 
identity={{
    href: '/',
    title: useCaseName,
    logo: {
        src: logo,
        alt: 'GeM Logo'
    }
}}
 
# remove policy alert
7.Remove the alert code from app.js
 
# Hyperlink
8.delete line 99 in chatMessage.tsx file.
(gaab-ui-change-like-dislike/source/ui-chat/src/components/ChatMessage.tsx)


#like dislike button
9. open the "chat.tsx" file and search for "MemoizedChatMessage" tag in "chat.tsx" file and replace with new tag. 
<MemoizedChatMessage
                                                key={index}
                                                message={message}
                                                messageIndex={index}
                                                messageList = {selectedConversation?.messages}
                                                displaySourceConfigFlag={displaySourceDocuments}
                                            />

10. add styling in chat.css file 



.thumbsContainer {
    display: flex;
    align-items: center;
    margin-top: 8px;
  }
  
  .thumbsIcon {
    cursor: pointer;
    margin-right: 10px;
    opacity: 0.5;
  }
  
  /* Styles for thumbs up icon. Should be compatible with dark theme */
  .thumbsUp {
    color: #539fe5;
  }
  
  /* Styles for thumbs down icon. Should be compatible with dark theme */
  .thumbsDown {
    color: #539fe5;
  }
  
  /* Style for clicked state */
  .clicked {
    opacity: 0.5;
    pointer-events: none; /* Disable pointer events for a clicked icon */
  }
  
  /* Styles for selected icon */
  .thumbsIcon.selected {
    opacity: 1 !important;
    pointer-events: none; /* Disable pointer events for the selected icon */
  }

11. Please follow the instructions below to modify the "chatMessage.tsx" file:
1. Open the "chatMessage.tsx" file.
2. Replace line number 17 with the following code: 
   import { Message, MessageWithSource } from '../types/chat';
3. Add the following lines to the import block:
   import './Chat.css';   import { v4 as uuidv4 } from "uuid";
4. Inside the `export interface` block, create a variable called `messageList`.
   Your `export interface` block should look like this:
   ```   export interface ChatMessageProps {      message: Message | MessageWithSource;      messageIndex: number;      displaySourceConfigFlag?: boolean;      messageList: any;   }   ```
5. replace export const ChatMessage with this

export const ChatMessage: FC<Props> = memo(({ message, messageIndex, displaySourceConfigFlag, messageList }) => {
    const {
        state: { selectedConversation, messageIsStreaming, loading }
    } = useContext(HomeContext);

    const [messagedCopied, setMessageCopied] = useState(false);
    const [docSourceModalVisible, setDocSourceModalVisible] = useState(false);
    const onModalDismiss = () => setDocSourceModalVisible(false);
    const [selectedIcon, setSelectedIcon] = useState<1 | 0 | null>(null);
    
    const copyOnClick = () => {
        if (!navigator.clipboard) return;

        navigator.clipboard.writeText(message.content).then(() => {
            setMessageCopied(true);
            setTimeout(() => {
                setMessageCopied(false);
            }, 2000);
        });
    };

    const displaySourceInResponse =
        displaySourceConfigFlag && message.sourceDocuments && message.sourceDocuments.length > 0;

        const showIcon = (messageIndex === (selectedConversation?.messages.length ?? 0) - 1) ? loading : false;

        const handleFeedback = (feedbackType: 1 | 0, idx: number, message: Message) => {
            
            const completion = message.content;
            const feedbackData: any = {
              sessionId: uuidv4() as string,
              feedback: feedbackType,
              answer: completion,
              question: messageList[idx-1].content
            };
            addUserFeedback(feedbackData);
          };
        
          const addUserFeedback =  (feedbackData: any) => {
            console.log(feedbackData);
            fetch('https://1tt8vzm8ze.execute-api.ap-south-1.amazonaws.com/dev/feeback/testtcs', {
                method: 'post',
                headers: {'Content-Type':'application/json'},
                body: JSON.stringify(feedbackData),
                mode: 'no-cors'
               })
               .then((response) => {
                 console.log(response);
               })
               .then((data) => {
                 console.log(data);
               })    
               .catch((err) => {
                 console.log(err.message);
               });
          };


    return (
        <div className={`${message.role === 'assistant' ? 'bg-gray-100' : ''}`} style={{ overflowWrap: 'anywhere' }}>
            <div className=" m-auto flex md:gap-6 md:py-6 xl:max-w-2xl">
                {message.role === 'assistant' && (
                    <div className="text-right font-bold ">
                        <Icon name={'contact'} />
                    </div>
                )}

                <div className="prose mt-[-2px] w-full">
                    {message.role === 'user' ? (
                        <div className="w-full">
                            <div>{message.content}</div>
                        </div>
                    ) : (
                        <>
                        <div className="flex ">
                            <div className="flex-1">
                                <MemoizedReactMarkdown
                                    className="prose dark:prose-invert flex-1"
                                    remarkPlugins={[remarkGfm, remarkMath]}
                                    unwrapDisallowed
                                    components={MARKDOWN_COMPONENTS}
                                >
                                    {`${message.content}${
                                        messageIsStreaming &&
                                        messageIndex === (selectedConversation?.messages.length ?? 0) - 1
                                            ? '▍'
                                            : ''
                                    }`}
                                </MemoizedReactMarkdown>
                            </div>
                            <div className="copy-button-div md:-mr-8">
                                {messagedCopied ? (
                                    <Icon name="status-positive" />
                                ) : (
                                    <div className="copy-button-div-nested">
                                        <Button
                                            data-testid="copy-msg-button"
                                            iconName="copy"
                                            variant="icon"
                                            onClick={copyOnClick}
                                        />
                                    </div>
                                )}
                                {displaySourceInResponse && (
                                    <div>
                                        <Button
                                            data-testid="source-docs-button"
                                            iconName="status-info"
                                            variant="icon"
                                            onClick={() => setDocSourceModalVisible(true)}
                                        />

                                        <SourceDocumentsModal
                                            visible={docSourceModalVisible}
                                            onDismiss={onModalDismiss}
                                            sourceDocumentsData={message.sourceDocuments}
                                        />
                                    </div>
                                )}
                            </div>
                        </div>
                        { (!showIcon) &&
                             (
                                <div className="thumbsContainer">
                                {(selectedIcon === 1 || selectedIcon === null) && (
                                  <Button
                                    variant="icon"
                                    iconName={selectedIcon === 1 ? "thumbs-up-filled" : "thumbs-up"}
                                    onClick={() => {
                                        handleFeedback(1, messageIndex, message)
                                      setSelectedIcon(1);
                                    }}
                                  />
                                )}
                                {(selectedIcon === 0 || selectedIcon === null) && (
                                  <Button
                                    iconName={
                                      selectedIcon === 0 ? "thumbs-down-filled" : "thumbs-down"
                                    }
                                    variant="icon"
                                    onClick={() => {
                                        handleFeedback(0, messageIndex, message)
                                      setSelectedIcon(0);
                                    }}
                                  />
                                )}
                              </div>

                            )
                        }
                        </>
                    )}
                </div>
                {message.role === 'user' && (
                    <div className="min-w-[40px] text-right">
                        <Icon name={'user-profile'} />
                    </div>
                )}
            </div>
        </div>
    );
});	 


12. npm i uuid
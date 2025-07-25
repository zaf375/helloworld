Import tkinter as tk
From tkinter import scrolledtext, messagebox
Import openai
From PIL import Image, ImageTk
Import requests
From io import BytesIO

# ==== Set your OpenAI API Key ====
Openai.api_key = “YOUR_API_KEY”

# ==== Function to get chat response ====
Def get_chat_response(prompt):
    Try:
        Response = openai.ChatCompletion.create(
            Model=”gpt-4”,  # or “gpt-3.5-turbo” if you have limited access
            Messages=[{“role”: “user”, “content”: prompt}]
        )
        Return response[‘choices’][0][‘message’][‘content’].strip()
    Except Exception as e:
        Return f”[Error: {str€}]”

# ==== Function to create image from prompt ====
Def create_image(prompt):
    Try:
        Response = openai.Image.create(
            Prompt=prompt,
            N=1,
            Size=”256x256”
        )
        Image_url = response[‘data’][0][‘url’]
        Return image_url
    Except Exception as e:
        Return None

# ==== Function to handle Send button ====
Def send_message():
    User_input = entry.get()
    If not user_input.strip():
        Return

    Chat_box.insert(tk.END, “You: “ + user_input + “\n”)

    If user_input.lower().startswith(“draw”) or user_input.lower().startswith(“generate image”):
        Image_prompt = user_input.replace(“draw”, “”).replace(“generate image”, “”).strip()
        Chat_box.insert(tk.END, “AI: Creating image for: “ + image_prompt + “\n”)
        Img_url = create_image(image_prompt)

        If img_url:
            Response = requests.get(img_url)
            Img_data = Image.open(BytesIO(response.content))
            Img = ImageTk.PhotoImage(img_data)
            Image_label.config(image=img)
            Image_label.image = img  # Keep a reference!
        Else:
            Chat_box.insert(tk.END, “AI: Failed to generate image.\n”)
    Else:
        Reply = get_chat_response(user_input)
        Chat_box.insert(tk.END, “AI: “ + reply + “\n”)

    Entry.delete(0, tk.END)

# ==== GUI Setup ====
Root = tk.Tk()
Root.title(“Interactive AI Chat + Image Creator”)

Chat_box = scrolledtext.ScrolledText(root, wrap=tk.WORD, width=60, height=20)
Chat_box.pack(padx=10, pady=10)

Entry = tk.Entry(root, width=50)
Entry.pack(side=tk.LEFT, padx=(10, 0), pady=10)

Send_button = tk.Button(root, text=”Send”, command=send_message)
Send_button.pack(side=tk.LEFT, padx=5, pady=10)

Image_label = tk.Label(root)
Image_label.pack(pady=10)

Root.mainloop()




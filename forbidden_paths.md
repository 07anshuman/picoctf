# Forbidden_paths Challenge

**Flag:** `picoCTF{7h3_p47h_70_5uccccess_e5a6fcbc}`

The first thing I did was open the networks page, checked the POST request for each .txt entered, found `filename=divine-comedy.txt&read=` edited the request and modified the body and filename to a relative path as suggested in the prompt. `filename=../../../../flag.txt&read=` for flag.txt in `/usr/share/nginx/html/`. I remembered exactly what it does because this was the only question I couldn't correctly answer in the first interview round after Linux Luminarium. Well, good for me I guess. 

![Screengrab]{forbidden_path.png}

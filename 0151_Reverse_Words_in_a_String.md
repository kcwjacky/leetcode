# 151. Reverse Words in a String
###### `medium` `Two Pointers` `strtok` `String`

---

Using strtok to split the input string into words.

## C

```cpp
char* reverseWords(char* s) {
    const char* delimiter = " ";
    char *p;
    p = strtok(s, delimiter);
    char **words = NULL;
    int num = 0;
    
    while (p != NULL) {
        words = realloc(words, ++num * sizeof(char *));
        words[num - 1] = strdup(p);   
        p = strtok(NULL, delimiter);	
    }

    size_t offset = 0;
    for (size_t i = 0; i < num; i++) {
        if (offset > 0)
            s[offset - 1] = ' ';
        char *ptrNextWord = words[num - 1 - i];
        strcpy(s + offset, ptrNextWord);
        offset += strlen(ptrNextWord);
        s[offset++] = '\0';
        free(ptrNextWord);
    }
    free(words);
    return s;
}
```

## CPP

Using stringstream to parse words.

```cpp
class Solution {
public:
    string reverseWords(string s) {
        string res;
        vector<string> words;
        stringstream ss(s);
        string word;
        while (ss >> word) 
            words.push_back(word);

        for (size_t i = 0; i < words.size(); i++) {
            if (i > 0)
                res.push_back(' ');
            res.append(words[words.size() - 1 - i]);
        }
        return res;
    }
};
```
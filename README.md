# nanojson
A JSON parser optimized for minimal memory usage in embedded applications. I wrote this for a project that had very limited RAM (2K). No heap allocations or copies are made. It's lazily written in C, it compiles on the modern compilers I tried but may need to be tweaked. It does almost no JSON validation, bad JSON could cause infinite loops/crashes. It is inspired by strtok() but doesn't use a global variable internally.

# Usage
json_obj() will decode a JSON object and json_arr() will iterate through a JSON array. You need to create a number of variables to loop over a JSON tree. Calling json_obj() again with new variables will allow you iterate nested trees (or you could store the v1begin variable and iterate it later. When encountering a type of JSON_TYPE_ARR, use json_arr() to iterate all the elements in the array. Don't touch the "state" variable, it's purpose is to eliminate the equivalent of the global variable in strtok().

Something like this is how I used it:

    char* jstate;
    unsigned char v1type;
    char* v1begin;
    int v1len;
    unsigned char v2type;
    char* v2begin;
    int v2len;
    char result = json_obj(&jstate, Buffer, &v1type, &v1begin, &v1len, &v2type, &v2begin, &v2len);
    
    while (result > 0)
    {
        //check v1type
        //do what you need to v1begin
        
        //iterate to the next item in the tree
		result = json_obj(&jstate, NULL, &v1type, &v1begin, &v1len, &v2type, &v2begin, &v2len);
    }
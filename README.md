//# distributor-location

#include <iostream>
#include <fstream>
#include <cstdlib>
#include <sstream>
#include <unordered_map>
#include <vector>
#include <string>
//#include <boost/algorithm/string.hpp>

using namespace std;

unordered_map<string,vector<vector<string>>> map_country;
unordered_map<string,vector<vector<string>>> map_required;
vector<string> v_in;
vector<string> v_in_new;
vector<string> v_ex;

int start = 0;


void uppercase(string &str)
{
    for(int i = 0; i<str.length(); i++)
    {
        str[i] = toupper(str[i]);
    }
}

vector<string> split(string input, char delimiter)
{
    vector<string> v_out;
    stringstream str(input);
    string text;
    while(getline(str,text,delimiter))
    {
        uppercase(text);
        v_out.push_back(text);
    }
    return v_out;
}

void check_input(string &user_string)
{
    cout << "Enter a location to check wheather it is available or not? " << endl << "(Press q to stop checking)" << endl;
    cout << "Add a space between multi word location name!" << endl;
    getline(cin,user_string); // >> user_string;
}

string type_check(string input, string &string_type)
{
    vector<string> v_temp;
    string out = "";
    v_temp = split(input, '-');
    if(v_temp.size() == 3)
    {
        string_type = "CITY";
    }
    else if(v_temp.size() == 2)
    {
        string_type = "PROVINCE";
    }
    else if(v_temp.size() == 1)
    {
        string_type = "COUNTRY";
    }
    if(v_temp.size() > 0)
        out = v_temp[0];
    return out;
}

bool process(string check_string,string string_type)
{
    bool result = 0;
    if(string_type == "COUNTRY")
    {
        if ( map_country.find(check_string) == map_country.end() )
        {
            cout << "Not found in csv!" << endl;
        }
        else if( map_required.find(check_string) == map_required.end() )
        {
            cout <<"Not found in map!!" << endl;

        }
        else
            result = 1;
    }
    if(string_type == "PROVINCE")
    {
        vector<string> v_split;
        v_split = split(check_string, '-');
        if ( map_country.find(v_split[1]) == map_country.end() )
        {
            cout << "Not found in csv!" << endl;
        }
        else if( map_required.find(v_split[1]) == map_required.end() )
        {
            cout <<"Not found in map" << endl;

        }
        else
        {
            //cout << " country present in map **" << endl;
            vector<vector<string>> v_province = map_required[v_split[1]];
           // cout << "size: " << v_province.size() << endl;
            for(unsigned int ptr = 0; ptr < v_province.size(); ptr++)
            {
                vector<string> v_state = v_province[ptr];
               // cout << v_state[0] << "," << v_state[1] << endl;
                if(v_state[0] == v_split[0])
                {
                    //cout << v_state[0] << "," << v_state[1] << endl;
                    result = 1;
                    break;
                }
            }
        }
    }
    if(string_type == "CITY")
    {
        vector<string> v_split;
        v_split = split(check_string, '-');
        //cout << "check_string: " << check_string << endl;
        if ( map_country.find(v_split[2]) == map_country.end() )
        {
            cout << "Not found in csv!" << endl;
        }
        else if( map_required.find(v_split[2]) == map_required.end() )
        {
            cout <<"Not found in map" << endl;

        }
        else
        {
            //cout << " country present in map" << endl;
            vector<vector<string>> v_province = map_required[v_split[2]];
            for(unsigned int ptr = 0; ptr < v_province.size(); ptr++)
            {
                vector<string> v_state = v_province[ptr];
               // cout << v_state[0] << "," << v_state[1] << endl;
                if(v_state[1] == v_split[0])
                {
                   // cout << v_state[0] << "," << v_state[1] << endl;
                    result = 1;
                }
            }
        }
    }
    return result;
}

void preparation()
{
    for(int itr = 0; itr < v_in.size(); itr++)
    {
        string str = v_in[itr];
        vector<string> v_split;
        v_split = split(str, '-');
        /*cout << "itr: " << itr << endl;
        for(int i = 0; i < v_split.size(); i++)
        {
            cout << v_split[i] << '\n';
        }
    */
        if(v_split.size() == 1)
        {
            map_required[v_split[0]] = map_country[v_split[0]];
        }
        else if(v_split.size() == 2)
        {
            vector<vector<string>> v_province = map_country[v_split[1]];
            //cout << "enter 2: " << v_split[0] << endl;
            //cout << "size: " << v_province.size() << endl;
            for(unsigned int ptr = 0; ptr < v_province.size(); ptr++)
            {
                vector<string> v_state = v_province[ptr];
                //cout << v_state[0] << "," << v_state[1] << endl;
                if(v_state[0] == v_split[0])
                {
                    map_required[v_split[1]].push_back(v_province[ptr]);
                }
            }
        }
        else if(v_split.size() == 3)
        {
            vector<vector<string>> v_province = map_country[v_split[2]];
            //cout << "enter 3: " << v_split[0] << endl;
            for(unsigned int ptr = 0; ptr < v_province.size(); ptr++)
            {
                vector<string> v_state = v_province[ptr];
               // cout << v_state[0] << "," << v_state[1] << endl;
                if(v_state[1] == v_split[0])
                {
                    map_required[v_split[2]].push_back(v_province[ptr]);
                }
            }
        }

    }
    int count = 0;
    for(int itr = 0; itr < v_ex.size(); itr++)
    {
        string str = v_ex[itr];
        vector<string> v_split;
        v_split = split(str, '-');
       /* cout << "ex itr: " << itr << endl;
        for(int i = 0; i < v_split.size(); i++)
        {
            cout << v_split[i] << '\n';
        }
        */
        if(v_split.size() == 1)
        {
            map_required.erase(v_split[0]);
        }
        else if(v_split.size() == 2)
        {
            vector<vector<string>> v_province = map_required[v_split[1]];
           // cout << "enter 2: " << v_split[0] << endl;
            //cout << "size: " << v_province.size() << endl;
            vector<vector<string>> v_new = v_province;
            for(unsigned int ptr = 0; ptr < v_province.size(); ptr++)
            {
                vector<string> v_state = v_province[ptr];
                vector<string> v_empty;
                v_empty.push_back("empty");
                v_empty.push_back("empty");
                //cout << v_state[0] << "," << v_state[1] << endl;
                if(v_state[0] == v_split[0])
                {
                    count++;
                    //cout << "ptr: " << ptr << endl;
                    //if(count > 10)
                   // exit(0);
                    //cout << v_state[0] << "," << v_state[1] << endl;
                   // v_new.erase(v_new.begin()+ptr);
                   v_province[ptr] = v_empty;
                }
            }
            //map_required[v_split[1]] = v_new;
            map_required[v_split[1]] = v_province;
            //cout << "size: " << v_province.size() << " , " << count << endl;
        }
        else if(v_split.size() == 3)
        {
            vector<vector<string>> v_province = map_required[v_split[2]];
           // cout << "enter 3: " << v_split[0] << endl;
            //cout << "size: " << v_province.size() << endl;
            vector<vector<string>> v_new = v_province;
            for(unsigned int ptr = 0; ptr < v_province.size(); ptr++)
            {
                vector<string> v_state = v_province[ptr];
                vector<string> v_empty;
                v_empty.push_back("empty");
                v_empty.push_back("empty");
               // cout << v_state[0] << "," << v_state[1] << endl;
                if(v_state[1] == v_split[0])
                {
                    //v_new.erase(v_new.begin()+ptr);
                    v_province[ptr] = v_empty;
                }
            }
           // map_required[v_split[2]] = v_new;
           map_required[v_split[2]] = v_province;
        }
    }

}

void get_userdata()
{
    string dis1, include, exclude, string_type, check_string;

    cout << "Permission for distributor: " << endl;

    if(start == 0)
    {
        cout << "Enter the name of the distributor: ";

        getline(cin,dis1);
    }
    start++;
    cout << "Add a space between multi word location name!" << endl;
    cout << "(press q to stop include/exclude)" << endl;

    //getline(cin,dis1);
    //uppercase(dis1);
    while(1)
    {
        if(start > 1)
        {
            cout << "Include: ";
            getline(cin,include);
            if(include == "q")
                break;
            uppercase(include);
            //cout << "in: " << include << endl;
            check_string = type_check(include, string_type);
            if(check_string != "")
            {
                uppercase(check_string);
                bool result = process(include,string_type);

                if(result == 1)
                {
                    //cout << "Present" << endl;
                    v_in_new.push_back(include);
                   // map_include[dis1].push_back(include);
                }
                else
                {
                    cout << "Not include in top distributor location!!!" << endl;
                }
            }
        }
        else
        {
            cout << "Include: ";
            getline(cin,include);
            if(include == "q")
                break;
            uppercase(include);
            v_in.push_back(include);
            //map_include[dis1].push_back(include);
        }
    }
    map_required.clear();
    while(1)
    {
        cout << endl << "Exclude: ";
        getline(cin,exclude);
        if(exclude == "q")
            break;
        uppercase(exclude);
        v_ex.push_back(exclude);
        //map_exclude[dis1].push_back(exclude);
    }
}

void check_location_exist()
{
    while(1)
    {
        string user_string,string_type,check_string;
        check_input(user_string);

        if(user_string == "q")
            break;

        uppercase(user_string);
        //cout << "user_string: " << user_string << endl;

        check_string = type_check(user_string, string_type);
        uppercase(check_string);

        //cout << "check_string: " << check_string << endl;
        //cout << "string_type: " << string_type << endl;

        bool result = process(user_string,string_type);

        if(result == 1)
        {
            cout << "Location is include" << endl;
        }
        else
        {
            cout << "Location is not available" << endl;
        }
    }
    char ans;
    cout << "whether you want to add sub-distributors? (Y/N)" << endl;
    cin >> ans;

    if(ans == 'Y' || ans == 'y')
    {
        //v_in = v_in_new;
        get_userdata();
        v_in = v_in_new;
        preparation();
        check_location_exist();
    }
    else
        exit(0);

    //return ans;
}

int main()
{
    string line;
    ifstream myfile;
    myfile.open("location.csv");
    //myfile.open("sample.csv");
    int count = 10;
    int num = 1;

    /*cout << "comes";
    if(myfile.is_open())
        cout << "yes";*/
    while(getline(myfile,line))
    {
        vector<string> v_temp;
        vector<string> v_result;
        v_temp = split(line,',');

        /*for(int i=0; i < v_temp.size(); i++)
        {
            cout << v_temp[i] << '\n';
        }*/

        v_result.push_back(v_temp[4]);
        v_result.push_back(v_temp[3]);

        if(num != 1)
        {
            map_country[v_temp[5]].push_back(v_result);
        }
        num++;
        /*count--;
        //cout << count << '\n';
        if(count != 0)
            cout << v_temp[5] << " , " << v_temp[4] << " - " << v_temp[3] << '\n';
        else
            break; */
    }
    myfile.close();
    //cout << "Map Size: " << map_country.size() << endl;
    //cout << "Num: " << num << endl;
    //cout << map_country["INDIA"].size() << endl;

    get_userdata();

    preparation();

    /*cout << "map_required Size: " << map_required.size() << endl;
    for(unordered_map<string,vector<vector<string>>>::iterator it=map_required.begin(); it != map_required.end(); ++it)
    {
        cout << it->first << ":" << it->second.size() << endl;
    }
    cout << "v_in size: " << v_in.size() << endl;
    cout << "v_ex size: " << v_ex.size() << endl;
    //exit(0);
    */
    check_location_exist();

    return 0;
}

# Getting Serious with Simple Examples -- Highest Population Increase 
## This is an exercise on re-organize row and column index, slicing, selecting and extracting dataset needed from a downloaded source.
### Key words of this exercise:
- String Functions
- MultiIndexing
- DatetimeIndex
- Boolean Indexing
- Index Extraction

### The explanation of the script 
1. Download data from the Australian Bureau of Statistics. if you have questuions on hoq to downloading data from webpage through pandas you can reference below page:
"A bit about debugging" section
[Reference on how to download webpage through pandas:](https://estherbaili.github.io/my-examples/)
```
url = 'https://www.ausstats.abs.gov.au/ausstats/ABS@archive.nsf/0/D5044B5F65AFD893CA25852F001DE5F5/$File/310104.xls'
population = pd.read_excel(url, sheet_name='Data1', index_col = 'Unnamed: 0', parse_dates=True)
```
2. Use string function to clean column index, make a multi-level column index : 'Estimated Resident Population' is redundant information, remove. 
Remove 'Male', 'Female', 'Person' and ';' and space using __str.replace()__
```
population.columns = population.columns.str.replace('Estimated Resident Population ;  ','').str.replace('Male ; ', '').str.replace('Female ; ','').str.replace('Persons ; ','').str.replace(';','').str.strip()
```
3. Making second level of column index, first create a list with the same length of list from step 2 using string operations, like doing operations on numbers, fantastic function!
```
a = ['Male']*9 + ['Female']*9 + ['Persons']*9
```
Making the multi-index:
```
tuples = list(zip(a, population.columns))
index = pd.MultiIndex.from_tuples(tuples, names=['first','second'])
population.columns = index
```
4. Transform index as DatatimeIndex, it will be a lot easier if need to select based on years/month/day/weekdat etc
```
population.index = pd.to_datetime(population.index)
```
5. Extract last 10 years subdataset:using __unique()__ and __boolean indexing__ from it extract Male dataset, and calculate percentage change
```
years10_1st = population.index.year.unique()[-11:][0]
population_10years= population.loc[population.index.year >= years10_1st]
population_10years_male = population_10years['Male']
changeRate_male_10 = population_10years_male.pct_change()
```
6. Getting max value is not hard, getting its corresponding index is not hard with __idxmax()__
```
changeRate_male_10_row = changeRate_male_10.max(axis = 1)
print(changeRate_male_10_row.idxmax())
changeRate_male_10_col = changeRate_male_10.max(axis = 0)
print(changeRate_male_10_col.idxmax())

changeRate_male_10_col.plot(kind = 'bar')
```
Mission Completed! It is in 2009-06-01 Northern Territory has the highest male population increase in the past 10 years !

![untitled](https://user-images.githubusercontent.com/44017368/81499156-91998400-9308-11ea-92c4-c0a11c77f2a4.png)




1.  allPromisesDone(promises: Promise<any>[]): Promise<void>
2.  capitalizeFirst(str: string): string
3.  chainErrorHandlers(handlers: ((error: Error) => void)[]): (error: Error) => void
4.  chunkifyArray<T>(arr: T[], chunkSize: number): T[][]
5.  deepMergeObjects<T1, T2>(obj1: T1, obj2: T2): T1 & T2
6.  filterNonUnique<T>(array: T[]): T[]
7.  findKeyByValue<T, K>(obj: T, predicate: (value: T[keyof T], key: keyof T) => boolean): keyof T | undefined
8.  flattenObjectPath(obj: any, delimiter?: string): any
9.  getFunctionArgs<T>(fn: (...args: any[]) => T): string[]
10. groupByMapValue<T, K>(array: T[], mapFn: (item: T) => K): Map<K, T[]>
11. isPromiseLike(obj: any): obj is Promise<any>
12. memoized<T>(fn: (...args: any[]) => T): (...args: any[]) => T
13. parseQueryString(queryString: string): Record<string, string>
14. pickByKey<T, K extends keyof T>(obj: T, keys: K[] | K): { [K in keyof T]: T[K] }
15. debounce<T>(fn: (...args: any[]) => T, delay: number): (...args: any[]) => void
16. throttle<T>(fn: (...args: any[]) => T, limit: number, interval: number): (...args: any[]) => void
17. shuffleArray<T>(array: T[]): T[]
18. camelize(str: string): string
19. deepFreeze<T>(obj: T): T
20. curry<T>(fn: (...args: any[]) => T, arity?: number): (...args: any[]) => T | ((...args: any[]) => T)
21. memoizeBy(keyFn: (args: any[]) => any, fn: (...args: any[]) => any): (...args: any[]) => any
22. groupBy(arr: T[], prop?: keyof T | ((item: T) => any)): Map<any, T[]>
23. deepFlatten<T>(arr: T[] | T[][] | T[][][]): T[]
24. interleave<T>(...arrs: T[][]): T[]
25. joinPaths(...paths: string[]): string
26. keysOfType<T, U>(obj: T, type: U): (keyof T)[]
27. readFileChunks(file: File, chunkSize: number = 1024): AsyncGenerator<ArrayBuffer, void, void>
28. reduceRightWhile<T>(arr: T[], reducer: (acc: T, curr: T) => T | undefined, initialValue: T): T | undefined
29. shuffleInPlace<T>(arr: T[]): void
30. groupByLongest(arr: string[], delim: string = ','): { [key: string]: string[] }
    31: throttleByArgs<T, R>(fn: (args: T) => R, delay: number, context?: ThisType<T>): (...args: T[]) => Promise<R>
    32: deepFilter<T>(obj: T, predicate: (value: T[keyof T]) => boolean): T | undefined
31. memoizeLast<T, R>(fn: (args: T) => R): (args: T) => R
32. zipWithIndex<T>(arr: T[]): (iterator: (value: T, index: number) => void) => void
33. parallelMap<T, R>(arr: T[], mapper: (item: T) => Promise<R>, concurrencyLimit?: number): Promise<R[]>
34. findPaths<T>(graph: { [key: string]: string[] }, start: string, end?: string): string[][]
35. keyByValue<T, K>(obj: { [key: string]: T }, keyFn: (value: T) => K): { [key: K]: T }
36. partitionWhile<T>(arr: T[], predicate: (item: T) => boolean): [T[], T[]]
37. shuffleInPlaceMutable<T>(arr: T[]): void
38. groupByCount<T>(arr: T[]): { [key: T]: number }
39. differenceBetweenArrays<T>(arr1: T[], arr2: T[]): T[]
40. generateRandomString
41. hasAllKeys<T>(obj: T, keys: string[]): boolean
42. pickKeys<T, K extends keyof T>(obj: T, keys: K[]): T[K] & Record<Exclude<keyof T, K>, undefined>
43. randomItem<T>(arr: T[]): T
44. sleep(ms: number): Promise<void>
45. retry<T>(fn: () => Promise<T>, options?: { attempts?: number, delay?: number, onFail?: (error: Error) => void }): Promise<T>
46. throttleConcurrent<T>(promises: Promise<T>[], maxConcurrency: number): Promise<T[]>
47. debouncedFetch<T>(url: string, options?: RequestInit): Promise<Response>
48. transformRecursively<T>(input: T | T[], predicate: (value: any) => boolean, newValue: any): T | T[]

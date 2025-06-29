---
tags:
  - react
---
links: [[custom hooks]]

---

#### Custom hook usado en Backups react+ts


Explicación de genericos aca:
Este custom hook usa una interface para retornar **FetchResponse**. Esta interface **FetchResponse** acepta un generico `<T>` para darle un tipo dinamico a la propiedad data.
En el useFetch, le damos a este custom hook un tipo generico Data, que va a ser el tipo de data a usar en el custom hook cuando se usa en algun componente. Este valor dado es pasado a la interface en el tipo de retorno, para que la propiedad data tenga el tipo de estructura que le damos en algun componente.
En resumen, el generico en este escenario es para hacer dinamico el tipo de dato de la propiedad data.

```tsx
interface FetchResponse<T> {
  data: T | null;
  isLoading: boolean;
  status: number | null;
  setStatus: (status: number | null) => void;
  error: string | null;
  fetchData: (url: string, options?: RequestInit) => Promise<void>;
}

/**
 * Custom fetch without useEffect included
 * @returns {Object} data, isLoading, status, error, fetchData
*/

export const useFetch = <Data = unknown>(): FetchResponse<Data> => {
  const [data, setData] = useState<Data | null>(null);
  const [isLoading, setIsLoading] = useState<boolean>(true);
  const [status, setStatus] = useState<number | null>(null);
  const [error, setError] = useState<string | null>(null);

  const fetchData = useCallback(async (url: string, options?: RequestInit) => {
    setIsLoading(true);
    setStatus(null);
    setError(null);
    setData(null);

    try {
      const response = await fetch(url, options);
  
      if (!response.ok) {
        const errorData = await response.json();
        setStatus(response.status);
        setError(typeof errorData === "string" ? errorData : JSON.stringify(errorData));

        return;
      }

      const result = await response.json();

      setStatus(response.status);
      setData(result);      
    } catch (error) {
      const errorMessage = error instanceof Error ? error.message : "Unknown error            (useFetch)";
      console.error(errorMessage);
      
      setError(errorMessage);
    } finally {
      setIsLoading(false);
    }
  }, []);

  return {
    data,
    isLoading,
    status,
    error,
    setStatus,
    fetchData,
  };


```
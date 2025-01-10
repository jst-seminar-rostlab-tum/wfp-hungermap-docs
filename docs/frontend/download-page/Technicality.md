## Technicality
```typescript
export const useDataQuery = (enabled: boolean) =>
  useQuery<DataType[]>(
    {
      queryKey: ['unique_key'],
      queryFn: repository.method,
      enabled,
    },
    cachedQueryClient
  );
```
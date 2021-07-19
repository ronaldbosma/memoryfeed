# Entity Framework FakeDbSet

Below is an example of a `FakeDbSet` that can be used to fake a .NET Framework `DbSet`.

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using System.Collections.ObjectModel;
using System.ComponentModel.DataAnnotations;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Linq.Expressions;
using System.Reflection;
using System.Threading;
using System.Threading.Tasks;

namespace FakeDbSetExample
{
    /// <summary>
    /// Masquerades a <code>List</code> as a <code>DbSet</code>.
    /// Use to fake access to the database when testing code that depends on it.
    /// </summary>
    /// <typeparam name="T">The type that defines the set</typeparam>
    public class FakeDbSet<T> : DbSet<T>, IQueryable, IEnumerable<T>, IDbAsyncEnumerable<T> where T : class
    {
        private readonly IQueryable _query;
        private readonly IEnumerable<PropertyInfo> _keys;

        /// <summary>
        /// The local collection
        /// </summary>
        public override ObservableCollection<T> Local { get; }

        /// <summary>
        /// Default constructor
        /// </summary>
        public FakeDbSet()
        {
            Local = new ObservableCollection<T>();
            _query = Local.AsQueryable();
            _keys = typeof(T).GetProperties()
                .Where(p => Attribute.IsDefined(p, typeof(KeyAttribute)) || "Id".Equals(p.Name, StringComparison.Ordinal))
                .ToList();
        }

        /// <summary>
        /// Finds a entity in the set
        /// </summary>
        public override T Find(params object[] keyValues)
        {
            if (keyValues == null)
            {
                throw new ArgumentNullException(nameof(keyValues));
            }
            if (keyValues.Any(k => k == null))
            {
                throw new ArgumentOutOfRangeException(nameof(keyValues));
            }
            if (keyValues.Length != _keys.Count())
            {
                throw new ArgumentOutOfRangeException(nameof(keyValues));
            }
            return Local.SingleOrDefault(i => _keys.Zip(keyValues, (k, v) => v.Equals(k.GetValue(i))).All(r => r));
        }

        /// <summary>
        /// Adds entity to the set
        /// </summary>
        public override T Attach(T entity)
        {
            Local.Add(entity);
            return entity;
        }

        /// <summary>
        /// Adds entity to the set
        /// </summary>
        public override T Add(T entity)
        {
            Local.Add(entity);
            return entity;
        }

        /// <summary>
        /// Adds a range of entities to the set
        /// </summary>
        public override IEnumerable<T> AddRange(IEnumerable<T> entities)
        {
            foreach (T entity in entities)
            {
                Local.Add(entity);
            }
            return entities;
        }

        /// <summary>
        /// Removes a entity from the set
        /// </summary>
        public override T Remove(T entity)
        {
            Local.Remove(entity);
            return entity;
        }

        /// <summary>
        /// Removes a range of entities from the set
        /// </summary>
        public override IEnumerable<T> RemoveRange(IEnumerable<T> entities)
        {
            foreach (T entity in entities.ToArray())
            {
                if (Local.Contains(entity))
                {
                    Remove(entity);
                }
            }
            return this;
        }

        /// <summary>
        /// Creates instance of type 
        /// </summary>
        public override TDerivedEntity Create<TDerivedEntity>()
        {
            return Activator.CreateInstance<TDerivedEntity>();
        }

        /// <summary>
        /// Creates instance
        /// </summary>
        /// <returns></returns>
        public override T Create()
        {
            return Activator.CreateInstance<T>();
        }

        /// <inheritdoc/>
        Type IQueryable.ElementType => _query.ElementType;

        /// <inheritdoc/>
        Expression IQueryable.Expression => _query.Expression;

        /// <inheritdoc/>
        IQueryProvider IQueryable.Provider => new AsyncQueryProviderWrapper<T>(_query.Provider);

        /// <inheritdoc/>
        IEnumerator IEnumerable.GetEnumerator()
        {
            return Local.GetEnumerator();
        }

        /// <inheritdoc/>
        IEnumerator<T> IEnumerable<T>.GetEnumerator()
        {
            return Local.GetEnumerator();
        }

        /// <inheritdoc/>
        public IDbAsyncEnumerator<T> GetAsyncEnumerator()
        {
            return new AsyncEnumeratorWrapper<T>(Local.GetEnumerator());
        }

        /// <inheritdoc/>
        IDbAsyncEnumerator IDbAsyncEnumerable.GetAsyncEnumerator()
        {
            return GetAsyncEnumerator();
        }
    }

    internal class AsyncEnumerableQuery<T> : EnumerableQuery<T>, IDbAsyncEnumerable<T>, IQueryable
    {
        public AsyncEnumerableQuery(IEnumerable<T> enumerable) : base(enumerable)
        {
        }

        public AsyncEnumerableQuery(Expression expression) : base(expression)
        {
        }

        public IDbAsyncEnumerator<T> GetAsyncEnumerator()
        {
            return new AsyncEnumeratorWrapper<T>(this.AsEnumerable().GetEnumerator());
        }

        IDbAsyncEnumerator IDbAsyncEnumerable.GetAsyncEnumerator()
        {
            return GetAsyncEnumerator();
        }

        IQueryProvider IQueryable.Provider
        {
            get { return new AsyncQueryProviderWrapper<T>(this); }
        }
    }

    internal class AsyncEnumeratorWrapper<T> : IDbAsyncEnumerator<T>
    {
        private readonly IEnumerator<T> _inner;

        public AsyncEnumeratorWrapper(IEnumerator<T> inner)
        {
            _inner = inner;
        }

        public void Dispose()
        {
            _inner.Dispose();
        }

        public Task<bool> MoveNextAsync(CancellationToken cancellationToken)
        {
            return Task.FromResult(_inner.MoveNext());
        }

        public T Current
        {
            get { return _inner.Current; }
        }

        object IDbAsyncEnumerator.Current
        {
            get { return Current; }
        }
    }

    internal class AsyncQueryProviderWrapper<T> : IDbAsyncQueryProvider
    {
        private readonly IQueryProvider _inner;

        internal AsyncQueryProviderWrapper(IQueryProvider inner)
        {
            _inner = inner;
        }

        public IQueryable CreateQuery(Expression expression)
        {
            return new AsyncEnumerableQuery<T>(expression);
        }

        public IQueryable<TElement> CreateQuery<TElement>(Expression expression)
        {
            return new AsyncEnumerableQuery<TElement>(expression);
        }

        public object Execute(Expression expression)
        {
            return _inner.Execute(expression);
        }

        public TResult Execute<TResult>(Expression expression)
        {
            return _inner.Execute<TResult>(expression);
        }

        public Task<object> ExecuteAsync(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute(expression));
        }

        public Task<TResult> ExecuteAsync<TResult>(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute<TResult>(expression));
        }
    }
}
```